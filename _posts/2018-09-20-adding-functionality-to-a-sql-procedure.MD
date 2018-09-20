---
layout: post
---
# Adding functionality to a SQL procedure

## So far

In the previous [blog](https://jamieo53.github.io/DevBlog/2018/09/19/procedure-and-test-development-in-sql-server.html) we developed a stored procedure together with a script to test it. We started with a statement of what we wanted to achieve, created the script to show the non-existent data to be created, then created the functionality to create the data, and finally we extracted that functionality into the desired procedure. We finished with our procedure, and a test for one scenario.

Now we will continue with its development.

### Test scenario

We need to add the Batcher specification. We create two batch objects, the Batch Profile and the File Profile. What makes this interesting is that batching happens on a separate staging database. For testing, we know exactly what that database is called, but in general we will not, so we need to introduce a SQLCMD variable for the purpose:

```sql
:SETVAR StagingDB gtmbedb
```

**Note**
When using SQLCMD features in SQL Studio Management Studio (SSMS) it is necessary to enable `SQL Mode`, which can be done by selecting the `Query/SQL Mode` menu item when the editor window is open.
**(End note)**

We will be creating the following:

* BatchProfile
  * batchProfile = BillingEvent:Event
  * schemaName = IF_BillingEvent
  * alias = BillingEvent:Event

* FileProfile
  * fileProfile = BillingEvent:Event

* BatchProfileMachine
  * Machine = Batch:Standard Batcher

## Starting

We start by copying the previous test script, and replacing the queries with queries that confirm the batch profile, file profile, and the batch profile machine:

```sql
:SETVAR StagingDB gtmbedb
BEGIN TRANSACTION

DECLARE
    @service AS VARCHAR (50),
    @companyCode AS VARCHAR (50),
    @coPartnerCode AS VARCHAR (50),
    @partnerCategory AS VARCHAR (50);

SELECT  @service = 'TEST',
        @companyCode = 'TST',
        @coPartnerCode = 'Ecs',
        @partnerCategory = 'StoreCode';

EXEC    BillingEvent.GenericBillingServiceSetup
    @service,
    @companyCode,
    @coPartnerCode,
    @partnerCategory

ROLLBACK
```

Note the $(StagingDB) variable.

We add the queries to test the values:

```sql
:SETVAR StagingDB gtmbedb
BEGIN TRANSACTION

DECLARE
    @service AS VARCHAR (50),
    @companyCode AS VARCHAR (50),
    @coPartnerCode AS VARCHAR (50),
    @partnerCategory AS VARCHAR (50);

SELECT  @service = 'TEST',
        @companyCode = 'TST',
        @coPartnerCode = 'Ecs',
        @partnerCategory = 'StoreCode';

EXEC    BillingEvent.GenericBillingServiceSetup
    @service,
    @companyCode,
    @coPartnerCode,
    @partnerCategory

SELECT  batchProfile, schemaName, alias
FROM    gtmbedb.Batch.BatchProfile
WHERE   batchProfile = 'BillingEvent:Event'

SELECT  fileProfile
FROM    gtmbedb.Batch.BatchProfile p
JOIN    gtmbedb.Batch.FileProfile fp
    ON  fp.batchProfileID = p.batchProfileID
WHERE   p.batchProfile = 'BillingEvent:Event'

SELECT  Machine
FROM    gtmbedb.Batch.BatchProfile p
JOIN    gtmbedb.Batch.BatchProfileMachine pm
    ON  pm.batchProfileID = p.batchProfileID
JOIN    gtmbedb.dbo.Machine m
    ON  m.machineID = pm.machineID
WHERE   p.batchProfile = 'BillingEvent:Event'

ROLLBACK
```

Learning from previous experience, the queries select only the values of interest, and don't use variables that could be used in the code.

## Adding the code

Following the procedure described in the previous blog, we create the missing data:

```sql
:SETVAR StagingDB gtmbedb
BEGIN TRANSACTION

DECLARE
    @service AS VARCHAR (50),
    @companyCode AS VARCHAR (50),
    @coPartnerCode AS VARCHAR (50),
    @partnerCategory AS VARCHAR (50);

SELECT  @service = 'TEST',
        @companyCode = 'TST',
        @coPartnerCode = 'Ecs',
        @partnerCategory = 'StoreCode';

EXEC    BillingEvent.GenericBillingServiceSetup
    @service,
    @companyCode,
    @coPartnerCode,
    @partnerCategory

DECLARE @batchProfileID INT,
        @fileProfileID INT,
        @machineID INT = $(StagingDB).dbo.MapMachine('Batch:Standard Batcher'),
        @batchProfile VARCHAR(50) = 'BillingEvent:Event'

EXEC    $(StagingDB).Batch.BatchProfile_Add
    @batchProfile = @batchProfile,
    @schemaName = 'IF_BillingEvent',
    @alias = @batchProfile,
    @batchProfileID = @batchProfileID OUTPUT

EXEC    $(StagingDB).Batch.FileProfile_Add
    @fileProfile = @batchProfile,
    @batchProfileID = @batchProfileID,
    @fileNameRegEx = '',
    @fileProfileID = @fileProfileID OUTPUT

EXEC    $(StagingDB).Batch.BatchProfileMachine_Add
    @batchProfileID = @batchProfileID,
    @machineID = @machineID

SELECT  batchProfile, schemaName, alias
FROM    gtmbedb.Batch.BatchProfile
WHERE   batchProfile = 'BillingEvent:Event'

SELECT  fileProfile
FROM    gtmbedb.Batch.BatchProfile p
JOIN    gtmbedb.Batch.FileProfile fp
    ON  fp.batchProfileID = p.batchProfileID
WHERE   p.batchProfile = 'BillingEvent:Event'

SELECT  Machine
FROM    gtmbedb.Batch.BatchProfile p
JOIN    gtmbedb.Batch.BatchProfileMachine pm
    ON  pm.batchProfileID = p.batchProfileID
JOIN    gtmbedb.dbo.Machine m
    ON  m.machineID = pm.machineID
WHERE   p.batchProfile = 'BillingEvent:Event'

ROLLBACK
```

The results are good:

| batchProfile | schemaName | alias |
|-|-|-|
| BillingEvent:Event | IF_BillingEvent | BillingEvent:Event |

| fileProfile |
|-|
| BillingEvent:Event |

| Machine |
|-|
| Batch:Standard Batcher |

## Moving the code to the procedure

Finally, we refactor the script and procedure, moving the code:

```sql
BEGIN TRANSACTION

DECLARE
    @service AS VARCHAR (50),
    @companyCode AS VARCHAR (50),
    @coPartnerCode AS VARCHAR (50),
    @partnerCategory AS VARCHAR (50);

SELECT  @service = 'TEST',
        @companyCode = 'TST',
        @coPartnerCode = 'Ecs',
        @partnerCategory = 'StoreCode';

EXEC    BillingEvent.GenericBillingServiceSetup1
    @service,
    @companyCode,
    @coPartnerCode,
    @partnerCategory

SELECT  batchProfile, schemaName, alias
FROM    gtmbedb.Batch.BatchProfile
WHERE   batchProfile = 'BillingEvent:Event'

SELECT  fileProfile
FROM    gtmbedb.Batch.BatchProfile p
JOIN    gtmbedb.Batch.FileProfile fp
    ON  fp.batchProfileID = p.batchProfileID
WHERE   p.batchProfile = 'BillingEvent:Event'

SELECT  Machine
FROM    gtmbedb.Batch.BatchProfile p
JOIN    gtmbedb.Batch.BatchProfileMachine pm
    ON  pm.batchProfileID = p.batchProfileID
JOIN    gtmbedb.dbo.Machine m
    ON  m.machineID = pm.machineID
WHERE   p.batchProfile = 'BillingEvent:Event'

ROLLBACK
```

and procedure, moving the code:

```sql
CREATE PROCEDURE [BillingEvent].[GenericBillingServiceSetup1]
    @service AS VARCHAR (50),
    @companyCode AS VARCHAR (50),
    @coPartnerCode AS VARCHAR (50),
    @partnerCategory AS VARCHAR (50)
AS
DECLARE @partner VARCHAR(50) = 'BillingEvent:' + @service
DECLARE @partnerID INT

EXEC    AddPartner
    @partner = @partner,
    @partnerID = @partnerID OUTPUT

EXEC    AddPartnerGroup
    @partnerID = @partnerID,
    @group = 'BillingEvent'

EXEC    AddPartnerMap
    @context = 'BillingEvent:Service',
    @aliasID = @service,
    @alias = @service,
    @partnerID = @partnerID

EXEC    AddPartnerMap
    @context = 'BillingEvent:Company',
    @aliasID = @companyCode,
    @alias = @companyCode,
    @partnerID = @partnerID

EXEC    AddPartnerMap
    @context = 'BillingEvent:CoPartner', 
    @aliasID = @coPartnerCode,
    @alias = @coPartnerCode,
    @partnerID = @partnerID

EXEC    AddPartnerMap
    @context = 'BillingEvent:PartnerCategory',
    @aliasID = @partnerCategory,
    @alias = @partnerCategory,
    @partnerID = @partnerID

DECLARE @batchProfileID INT,
        @fileProfileID INT,
        @machineID INT = $(StagingDB).dbo.MapMachine('Batch:Standard Batcher'),
        @batchProfile VARCHAR(50) = 'BillingEvent:Event'

EXEC    $(StagingDB).Batch.BatchProfile_Add
    @batchProfile = @batchProfile,
    @schemaName = 'IF_BillingEvent',
    @alias = @batchProfile,
    @batchProfileID = @batchProfileID OUTPUT

EXEC    $(StagingDB).Batch.FileProfile_Add
    @fileProfile = @batchProfile,
    @batchProfileID = @batchProfileID,
    @fileNameRegEx = '',
    @fileProfileID = @fileProfileID OUTPUT

EXEC    $(StagingDB).Batch.BatchProfileMachine_Add
    @batchProfileID = @batchProfileID,
    @machineID = @machineID
```

Executing the script, we get the same result.