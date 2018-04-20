---
layout: post
---
# Revitalizing a SQL Server Database: Making a Start

## Introduction

Database development suffers from archaic development processes. Modern development tools are now able to bring their full power to bear on the problems faced by database developers. In these blogs I demonstrate how Microsoft's Visual Studio can be used to make database development thoroughly modern.

## Motivation

I have often struggled with complex monolithic corporate databases. They are hard to understand because everything is thrown together and mixed up. I always get to the point where I wish that I could make use of modern programming tools.

Well, Microsoft's [SQL Server Data Tools (SSDT)](https://www.visualstudio.com/vs/features/ssdt/) makes this possible. The essential feature is the [Visual Studio Database Project](https://msdn.microsoft.com/en-us/library/hh272702(v=vs.103).aspx).

The argument against change is that "We have our legacy database, and there is no way that we can use these tools on it. The risk is just too great."

I disagree. In these blogs I will demonstrate that these tools can be used to safely make a legacy database thoroughly modern and make the best modern tools available to make database development safe and reliable.

## The benefits

These blogs will show how database developers can get the benefits that other developers have come to expect.

* The developer can work in a modern *integrated development environment* (IDE)
* The database objects are individually under source control.
  * A history of changes to each object are easily accessible.
  * Copies of the application can be branched off and can be worked on in isolation.
  * The code base in production, undergoing *user acceptance testing* (UAT), and in development are separated.
* The database is divided into logical subsystems that can be worked on in isolation.
  * Unit testing can ensure that the subsystems work as intended.
* Features can be reused between databases.
  * Features can be versioned, allowing their deployment to be delayed.
  * Dependencies between features can be specified so that deployment can be synchronized.
  * Well tested components can be dropped into tailored applications - the product lines model.

## Making a start

For realistic examples, I have found an open source project I can use. It had to use SQL Server. Ideally it was not being actively supported. I wanted something that could be a business application developed over some years with shifting requirements. A moribund open source project seemed like a good stand-in for such an application.

I found [OpenMiracle](https://sourceforge.net/projects/openmiracle/) which has a nice mix of features. That it was SQL Server 2005 database and without any recent commits made it particularly
appealing. This is the sort of system I have had to work on. I will use it to illustrate my posts.

My copy is in [GitHub](https://github.com/JamieO53/OpenMiracle). I will be adding my examples to this project.

I have installed the database on my computer. Importing the database into Visual Studio had the immediate benefit of identifying code that could not possibly work, and I could get rid of that. I have the details here for anybody who wants to follow my steps.

[Setting up the database](../assets/pages/2018-04/2018-04-19-setting-up-the-database.md)