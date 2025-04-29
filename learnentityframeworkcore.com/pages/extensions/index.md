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