---
layout: post
---
# Using Visual Studio SQL Server projects to improve the cohesion of a SQL Server database

I have often struggled with complex monolithic corporate databases. They are hard to understand because everything is thrown together and mixed up. I always get to the point where I wish that the database could be divided up into cohesive modules, where everything in a module belongs together.

Well, Microsoft's [SQL Server Data Tools (SSDT)](https://www.visualstudio.com/vs/features/ssdt/) makes this possible. The essential feature is the [Visual Studio Database Project](https://msdn.microsoft.com/en-us/library/hh272702(v=vs.103).aspx).

For illustration, I looked around GigHub and Sourceforge for fairly old projects using SQL Server. I found [OpenMiracle](https://sourceforge.net/projects/openmiracle/) which has a nice mix of features. I cloned the code to my [GitHub repository](https://github.com/JamieO53/OpenMiracle). I will use the project to provide examples in my devblog posts.

## Database Refactoring

### Extract Subsystem

You have a database project with poor cohesion and have identified a subsystem with better cohesion.

_Create a new Database Solution and move the subsystem to the new solution_

#### Motivation

Databases are traditionally created with all objects in the same space which can be overwhelming. Visual Studio Database projects allow a database to be broken up into cohesive parts. This also presents us with an opportunity to reuse database objects in different databases.

#### Mechanics

* Create a new Database Solution to contain the subsystem.
* Publish the empty solution to NuGet.
* Add the NuGet reference to the original Database Solution.
* Get the package content for the original Database Solution.
* Add the database reference to the subsystem database to the original database project.
* Build, publish and test the original database solution.
* For each table in the subsystem:
  * Add Nuget references for any referenced subsystems.
  * Get the package content for the subsystem database solution.
  * Add the database references to the subsystem SQL project.
  * Add the table to the subsystem SQL project.
  * Add the CRUD routines for the table to the SQL project.
  * Add tests for the CRUD routines to the 