---
layout: post
---
# Procedure and Test Development in SQL Server

## The Example

The system under test (SUT) will be added to a number of database applications to create daily batches of transaction summaries for billing our clients. The applications already have common code for processing batches and manipulating the state of transactions. This example will make use of these features without explanation. Hopefully the usage will be obvious.

Each client can be billed for a number of services, and each service is identified as a partner, and service partners belong to the `BillingEvent` partner group. The `PartnerMap` table needs to be populated for the inner working of the applications.

The Batcher is configured for the batches being created.

Two state machines are defined to identify billable transactions (`Dispatcher`), and to track the billing transactions created (`EventTracker`). These machines define the valid state transitions available to transactions.

## Objective

We will be creating a script to create the stored procedure, and a number of scripts that show the data created by the procedure. In this blog we focus on the `Partner` configuration.

## Test scenario

For testing, we will use the following variables:

```sql
DECLARE
    @service            VARCHAR(50) = 'TEST',
    @companyCode        VARCHAR(50) = 'TST',
    @coPartnerCode      VARCHAR(50) = 'Ecs',
    @partnerCategory    VARCHAR(50) = 'StoreCode'
```

We will be creating the following:

+ Partner
  + BillingEvent:TEST
+ PartnerGroup
  + BillingEvent
+ PartnerMap
  + BillingEvent:Company = TST
  + BillingEvent:CoPartner = Ecs
  + BillingEvent:PartnerCategory = StoreCode
  + BillingEvent:Service = TEST

### Create a working script

We test this with these queries:

```sql
DECLARE @partnerID INT = dbo.MapPartner('BillingEvent:TEST')

SELECT  *
FROM    dbo.Partner
WHERE   partnerID = @partnerID

SELECT  *
FROM    dbo.PartnerGroup
WHERE   partnerID = @partnerID

SELECT  *
FROM    dbo.PartnerMap
WHERE   partnerID = @partnerID
    AND context LIKE 'BillingEvent:%'
ORDER BY context
```

At this stage we will be testing by looking at the result. We will be running our test repeatedly, so we put it in a transaction that we can roll back. Our script now looks like this:

```sql
BEGIN TRANSACTION

DECLARE
    @service            VARCHAR(50) = 'TEST',
    @companyCode        VARCHAR(50) = 'TST',
    @coPartnerCode      VARCHAR(50) = 'Ecs',
    @partnerCategory    VARCHAR(50) = 'StoreCode'

-- something will go here

DECLARE @partnerID INT = dbo.MapPartner('BillingEvent:TEST')

SELECT  *
FROM    dbo.Partner
WHERE   partnerID = @partnerID

SELECT  *
FROM    dbo.PartnerGroup
WHERE   partnerID = @partnerID

SELECT  *
FROM    dbo.PartnerMap
WHERE   partnerID = @partnerID
    AND context LIKE '%'
ORDER BY context

ROLLBACK
```

### Implementing the functionality

We get each part to work in turn.

#### Partner

We create a partner making use of the parameters:

```sql
DECLARE @partner VARCHAR(50) = 'BillingEvent:' + @service
DECLARE @partnerID INT
EXEC    AddPartner @partner = @partner, @partnerID = @partnerID OUTPUT

--DECLARE   @partnerID INT = dbo.MapPartner('BillingEvent:TEST')
SET     @partnerID = dbo.MapPartner('BillingEvent:TEST')
```

Note that I have temporarily replaced the declaration of `@partnerID` with a `SET` command. My plan is to refactor the configuration code into a procedure, but for now it is convenient to do our coding inline.

When we execute the script, we get the following output:

partnerID|partner|enabled|dateEnabledChanged
-|-|-|-
12|BillingEvent:TEST|1|2018-09-19 13:28:40.657

group|partnerID
-|-

 context|aliasID|alias|partnerID
-|-|-|-

Later we will automate the test by comparing the result with our expectation. Now will be a good time to tidy up the queries to show only the values we want to compare:

```sql
SELECT  partner
FROM    dbo.Partner
WHERE   partnerID = @partnerID

SELECT  [group] partnerGroup
FROM    dbo.PartnerGroup
WHERE   partnerID = @partnerID

SELECT  context, aliasID, alias
FROM    dbo.PartnerMap
WHERE   partnerID = @partnerID
    AND	context LIKE 'BillingEvent:%'
ORDER BY context
```

yielding

| partner |
|-|
| BillingEvent:TEST |

| partnerGroup |
|-|

| context|aliasID|alias |
|-|-|-|

#### Partner Group

We add

```sql
EXEC    AddPartnerGroup @partnerID = @partnerID, @group = 'BillingEvent'
```

yielding

| partner |
|-|
| BillingEvent:TEST |

| partnerGroup |
|-|
|BillingEvent|

| context|aliasID|alias |
|-|-|-|

#### Partner Maps

To see what happens, we add

```sql
EXEC    AddPartnerMap @context = 'BillingEvent:Service', @aliasID = @service, @alias = @service, @partnerID = @partnerID
```

and get

| partner |
|-|
| BillingEvent:TEST |

| partnerGroup |
|-|
|BillingEvent|

| context|aliasID|alias |
|-|-|-|
BillingEvent:Service|TEST|TEST

Looking good! We add the remaining partner maps and get

| partner |
|-|
| BillingEvent:TEST |

| partnerGroup |
|-|
|BillingEvent|

| context|aliasID|alias |
|-|-|-|
BillingEvent:Company|TST|TST
BillingEvent:CoPartner|Ecs|Ecs
BillingEvent:PartnerCategory|StoreCode|StoreCode
BillingEvent:Service|TEST|TEST

### The test script

Our script now looks like this:

```sql
BEGIN TRANSACTION

DECLARE @service AS VARCHAR (50), @companyCode AS VARCHAR (50), @coPartnerCode AS VARCHAR (50), @partnerCategory AS VARCHAR (50);

SELECT  @service = 'TEST',
        @companyCode = 'TST',
        @coPartnerCode = 'Ecs',
        @partnerCategory = 'StoreCode';

DECLARE @partner VARCHAR(50) = 'BillingEvent:' + @service
DECLARE @partnerID INT
EXEC    AddPartner @partner = @partner, @partnerID = @partnerID OUTPUT
EXEC    AddPartnerGroup @partnerID = @partnerID, @group = 'BillingEvent'
EXEC    AddPartnerMap @context = 'BillingEvent:Service', @aliasID = @service, @alias = @service, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:Company', @aliasID = @companyCode, @alias = @companyCode, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:CoPartner', @aliasID = @coPartnerCode, @alias = @coPartnerCode, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:PartnerCategory', @aliasID = @partnerCategory, @alias = @partnerCategory, @partnerID = @partnerID

--DECLARE   @partnerID INT = dbo.MapPartner('BillingEvent:TEST')
SET     @partnerID = dbo.MapPartner('BillingEvent:TEST')

SELECT  partner
FROM    dbo.Partner
WHERE   partnerID = @partnerID

SELECT	[group] partnerGroup
FROM    dbo.PartnerGroup
WHERE   partnerID = @partnerID

SELECT  context, aliasID, alias
FROM    dbo.PartnerMap
WHERE   partnerID = @partnerID
    AND	context LIKE 'BillingEvent:%'
ORDER BY context

ROLLBACK
```

### Extracting the procedure

We replace the code

```sql
DECLARE @partner VARCHAR(50) = 'BillingEvent:' + @service
DECLARE @partnerID INT
EXEC    AddPartner @partner = @partner, @partnerID = @partnerID OUTPUT
EXEC    AddPartnerGroup @partnerID = @partnerID, @group = 'BillingEvent'
EXEC    AddPartnerMap @context = 'BillingEvent:Service', @aliasID = @service, @alias = @service, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:Company', @aliasID = @companyCode, @alias = @companyCode, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:CoPartner', @aliasID = @coPartnerCode, @alias = @coPartnerCode, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:PartnerCategory', @aliasID = @partnerCategory, @alias = @partnerCategory, @partnerID = @partnerID
```

with

```sql
EXECUTE [BillingEvent].[GenericBillingServiceSetup] @service, @companyCode, @coPartnerCode, @partnerCategory;
```

and uncomment

```sql
--DECLARE   @partnerID INT = dbo.MapPartner('BillingEvent:TEST')
```

to yield

```sql
BEGIN TRANSACTION

-- database unit test for BillingEvent.GenericBillingServiceSetup
DECLARE @service AS VARCHAR (50), @companyCode AS VARCHAR (50), @coPartnerCode AS VARCHAR (50), @partnerCategory AS VARCHAR (50);

SELECT  @service = 'TEST',
        @companyCode = 'TST',
        @coPartnerCode = 'Ecs',
        @partnerCategory = 'StoreCode';

EXEC [BillingEvent].[GenericBillingServiceSetup] @service, @companyCode, @coPartnerCode, @partnerCategory;

DECLARE @partnerID INT = dbo.MapPartner('BillingEvent:TEST')
SET     @partnerID = dbo.MapPartner('BillingEvent:TEST')

SELECT  partner
FROM    dbo.Partner
WHERE   partnerID = @partnerID

SELECT  [group] partnerGroup
FROM    dbo.PartnerGroup
WHERE   partnerID = @partnerID

SELECT  context, aliasID, alias
FROM    dbo.PartnerMap
WHERE   partnerID = @partnerID
    AND	context LIKE 'BillingEvent:%'
ORDER BY context

ROLLBACK
```

The stored procedure is like this

```sql
CREATE PROCEDURE [BillingEvent].[GenericBillingServiceSetup]
    @service AS VARCHAR (50),
    @companyCode AS VARCHAR (50),
    @coPartnerCode AS VARCHAR (50),
    @partnerCategory AS VARCHAR (50)
AS
DECLARE @partner VARCHAR(50) = 'BillingEvent:' + @service
DECLARE @partnerID INT
EXEC    AddPartner @partner = @partner, @partnerID = @partnerID OUTPUT
EXEC    AddPartnerGroup @partnerID = @partnerID, @group = 'BillingEvent'
EXEC    AddPartnerMap @context = 'BillingEvent:Service', @aliasID = @service, @alias = @service, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:Company', @aliasID = @companyCode, @alias = @companyCode, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:CoPartner', @aliasID = @coPartnerCode, @alias = @coPartnerCode, @partnerID = @partnerID
EXEC    AddPartnerMap @context = 'BillingEvent:PartnerCategory', @aliasID = @partnerCategory, @alias = @partnerCategory, @partnerID = @partnerID
```

Running the script yields

| partner |
|-|
| BillingEvent:TEST |

| partnerGroup |
|-|
|BillingEvent|

| context|aliasID|alias |
|-|-|-|
BillingEvent:Company|TST|TST
BillingEvent:CoPartner|Ecs|Ecs
BillingEvent:PartnerCategory|StoreCode|StoreCode
BillingEvent:Service|TEST|TEST

which hits the nail on the head!