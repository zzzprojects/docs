---
title: Extensions for EF Core | Third Party Libraries
description: An introduction to third-party libraries for EF Core
canonical: /extensions
status: Published
lastmod: 2025-10-16
---

# Extensions

Here’s a curated list of popular third-party extensions that make EF Core even more powerful — from bulk operations to auditing and cloning.

EF Core has multiple third-party libraries available to enhance your experience.

One of the most notable is [Entity Framework Extensions](https://entityframework-extensions.net/), which we proudly promote on this website — since we’re also the creators!

---

## Entity Framework Extensions

**Boost EF Core performance by up to 95% — with professional support and full documentation.**

[Entity Framework Extensions](https://entityframework-extensions.net/) is the **most advanced and reliable bulk operations library for EF Core**.  
It extends your DbContext with methods like `BulkInsert`, `BulkUpdate`, `BulkDelete`, `BulkMerge`, and `BulkSaveChanges`, allowing you to process thousands or even millions of records efficiently.

Developed and maintained by [ZZZ Projects](https://zzzprojects.com/) since **2014**, it includes **hundreds of options**, **monthly updates**, and **dedicated professional support**.  
Trusted by **thousands of companies worldwide**, it remains the go-to choice for teams that need both **speed and reliability**.

[Read more](/extensions/entity-framework-extensions)

---

## Audit.EntityFramework.Core

**Entity Framework Audit Extension for the [Audit.NET Library](https://github.com/thepirat000/Audit.NET)**

This extension automatically generates audit logs for your Entity Framework operations. It supports both Entity Framework and Entity Framework Core.

You can use this library to log all interactions with your `DbContext`, including detailed information about each CRUD operation made on your database.

[Read more](/extensions/audit-entityframework-core)

---

## EntityCloner.Microsoft.EntityFrameworkCore

Easily clone EF Core entities—including related data—based on your model configuration.  

This library resets keys and concurrency tokens, supports composite keys, and works with single entities, queries, and collections.  

Perfect for duplicating data without tracking conflicts.

[Read more](/extensions/entitycloner-microsoft-entityframeworkcore)

---

## EntityFrameworkCore.SqlServer.NodaTime

Seamlessly integrates NodaTime types with EF Core and SQL Server.

Supports advanced querying and native translation for types like `Instant`, `LocalDate`, `OffsetDateTime`, and more.

Enables use of SQL functions like `DATEADD`, `DATEDIFF`, and `DATEPART` directly in LINQ queries.

Ideal for developers needing precise date/time handling in modern .NET apps.

[Read more](/extensions/entityframeworkcore-sqlserver-nodatime)

---

## EntityFrameworkCore.Sqlite.NodaTime

**EntityFrameworkCore.Sqlite.NodaTime** is a library that adds support for NodaTime types when using SQLite with Entity Framework Core.  
With a simple `UseNodaTime()` configuration, developers can store and query temporal data like `Instant`, `LocalDateTime`, and `LocalDate` directly in SQLite.  
It automatically translates NodaTime properties and methods into optimized SQL expressions, making it easy to work with date and time values in a robust and type-safe way.

[Read more](/extensions/entityframeworkcore-sqlite-nodatime)

---

## EFCore.BulkExtensions

**Originally free and open source, now under a dual-license model.**

[EFCore.BulkExtensions](https://github.com/borisdj/EFCore.BulkExtensions) was once a popular choice for EF Core bulk operations.  
It now requires a **commercial license for companies earning over $1M annually**, and the open-source version is no longer actively maintained.

Today, most developers choose either the **open-source [EFCore.BulkExtensions.MIT](https://github.com/videokojot/EFCore.BulkExtensions.MIT)** fork or the **professional [Entity Framework Extensions](https://entityframework-extensions.net/)** library for better performance and long-term support.

[Read more](/extensions/efcore-bulkextensions)

---

## EFCore.BulkExtensions.MIT

**The MIT-licensed fork of EFCore.BulkExtensions.**

[EFCore.BulkExtensions.MIT](https://github.com/videokojot/EFCore.BulkExtensions.MIT) is a **community fork** created to preserve a truly open-source version of the original library.  
It provides the same familiar syntax for `BulkInsert`, `BulkUpdate`, and `BulkDelete`, remaining compatible with modern EF Core versions.  

Ideal for developers who prefer an **MIT-licensed, no-restriction alternative**, or for projects that value open-source alignment.

[Read more](/extensions/efcore-bulkextensions-mit)

---

## Summary

Each library listed here has its own strengths — choose the one that best fits your project’s **scale**, **licensing needs**, and **performance requirements**.