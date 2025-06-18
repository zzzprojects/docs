---
title: Extensions for EF Core | Third Party Libraries
description: An introduction to third-party libraries for EF Core
canonical: /extensions
status: Published
lastmod: 2025-04-05
---

# Extensions

EF Core has multiple third-party libraries available to enhance your experience.

One of the most notable is [Entity Framework Extensions](https://entityframework-extensions.net/), which we proudly promote on this website—since we’re also the creators!

## Audit.EntityFramework.Core

**Entity Framework Audit Extension for the [Audit.NET library](https://github.com/thepirat000/Audit.NET)**

This extension automatically generates audit logs for your Entity Framework operations. It supports both Entity Framework and Entity Framework Core.

You can use this library to log all interactions with your `DbContext`, including detailed information about each CRUD operation made on your database.

[Read more](/extensions/audit-entityframework-core)

## EntityCloner.Microsoft.EntityFrameworkCore

Easily clone EF Core entities—including related data—based on your model configuration. 

This library resets keys and concurrency tokens, supports composite keys, and works with single entities, queries, and collections. 

Perfect for duplicating data without tracking conflicts.

[Read more](/extensions/entitycloner-microsoft-entityframeworkcore)

## EntityFrameworkCore.SqlServer.NodaTime

Seamlessly integrates NodaTime types with EF Core and SQL Server.

Supports advanced querying and native translation for types like `Instant`, `LocalDate`, `OffsetDateTime`, and more.

Enables use of SQL functions like `DATEADD`, `DATEDIFF`, and `DATEPART` directly in LINQ queries.

Ideal for developers needing precise date/time handling in modern .NET apps.

[Read more](/extensions/entityframeworkcore-sqlserver-nodatime)

## EntityFrameworkCore.Sqlite.NodaTime

**EntityFrameworkCore.Sqlite.NodaTime** is a library that adds support for NodaTime types when using SQLite with Entity Framework Core. With a simple `UseNodaTime()` configuration, developers can store and query temporal data like `Instant`, `LocalDateTime`, and `LocalDate` directly in SQLite. It automatically translates NodaTime properties and methods into optimized SQL expressions, making it easy to work with date and time values in a robust and type-safe way.

[Read more](/extensions/entityframeworkcore-sqlite-nodatime)