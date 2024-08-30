---
permaid: 1000223
Title: EF Core Overview - Learn What is EF Core and Why You Should Use it
MetaDescription: Unlock the power of EF Core by understanding the basics and more advanced topics of this library. Learn what EF Core is, how it works, and all the documentation you need to get started.
LastMod: 2024-08-30
tags: getting-started history
---

# EF Core Overview: Discover What is EF Core and Why You Should Use it

## Introduction

Entity Framework Core is a lightweight, extensible and cross-platform version of Microsoft's object-relational mapper (ORM), Entity Framework is the official data access platform for Microsoft. 

 - As .NET evolves towards a lighter weight cross-platform environment, Entity Framework is making the same transition. 
 - In other words, this was not simply an update from the latest Entity Framework (EF6), but it is a different kind of Entity Framework. 
 - It includes a set of .NET APIs for performing data access in your application. 

## History

 - EF team has made some significant changes over Entity Framework 6 and called it EF7, but later they rename it to EF Core.
 - EF Core was first released in late June of 2016 after over 2 years of effort, this first version was followed by a great amount of change with EF Core 2, which was released a year later along with .NET Core and ASP.NET Core. 
 - EF Core 2 is seen by many as the first solid version, although EF Core 1 is still supported.

## Why Entity Framework Core

 - EF Core is an object-relational mapper (ORM) that enables .NET developers to work with a database using .NET objects.
 - It is a lightweight, extensible and can be used across various platforms
 - It eliminates the need for most of the data-access code that developers usually need to write. 
 - Entity Framework Core 2 brings more features that many developers needed in earlier versions of EF, as well as new innovations beyond Entity Framework Core 1.

## Supported Database Providers

Entity Framework Core supports many database providers to access different databases and perform database operations.

 - SQL Server
 - MySQL
 - MariaDB
 - PostgreSQL
 - SQLite
 - SQL Compact
 - In-memory

More provider can be found here: <a href="https://docs.microsoft.com/en-us/ef/core/providers/" target="_blank">https://docs.microsoft.com/en-us/ef/core/providers/</a>

## EF Core 2 New Features

##### LINQ

 - LINQ queries have been made more efficient and LINQ querying methods will be able to become part of the SQL that's run on the database, rather than left for post-query client-side evaluation. 

##### Filters

 - It is now possible to define query filters in your DbContext for individual entities, and these filters will be applied to any queries that your app is executing before that query goes to the database. 

##### GroupJoin Improvements

 - If you use GroupJoin in your queries, the generated SQL is much smarter in EF Core 2 than it was in the first version of EF Core. 

##### Mappings

 - Mapping database scalar functions to method stub so that they can be used in LINQ queries and translated to SQL.
 - In EF Core 1, there was no support for value objects, but now in EF Core 2, we have something better that replaces complex types and it's called owned entities. 

##### Table Splitting

 - Table splitting is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.

##### DbContext pooling
 
- DbContext pooling is a really cool performance feature that allows EF Core at runtime to use a pool of pre-created DbContext instances, rather than instantiating a new context every time.

##### Others

 - In EF Core, most of the logic that is used under the covers is no longer hidden and EF Core is made up of hundreds of services that you can override with your own versions, and with EF Core 2, it's now a lot easier to tell EF Core how and when to use your custom overrides for these services. 
