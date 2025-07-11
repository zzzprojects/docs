---
title: EF Core BulkExtensions, Bulk Insert, Update, Delete, Read, SaveChanges
description: Learn to use Bulk Extensions in EF Core. Improve your .NET Core performance with bulk extensions methods.
canonical: /bulk-extensions
status: Published
lastmod: 2025-07-11
---

# Bulk Extensions with EF Core

Entity Framework Core (EF Core) is well-known for being a great ORM (Object-Relational Mapping) tool. It makes saving entities to your database super easy—without writing any SQL statements.

But when you need to handle thousands of entities, EF Core's performance can hit a wall. That’s where **Bulk Extensions**, a feature included in the [Entity Framework Extensions](https://entityframework-extensions.net/) library, comes in. This powerful library helps dramatically boost the performance of EF Core.

```csharp
// Easy to use
context.BulkInsert(customers);
context.BulkUpdate(customers);
context.BulkDelete(customers);
context.BulkMerge(customers);
context.BulkSynchronize(customers);

// Easy to customize
context.BulkInsert(customers, options => options.IncludeGraph = true);
```

### Why Use Bulk Extensions?

- ✅ **Easy to use** – The syntax is simple and intuitive  
- ✅ **Easy to customize** – Over 100 options available to fit your needs  
- ✅ **Supports all EF Core versions**  
- ✅ **Supports all kinds of models** – Inheritance, complex types, owned entity types, value converters, etc.  
- ✅ **Database agnostic** – Works with SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle

### Performance

One of the biggest advantages of using Bulk Extensions is the massive performance improvement it brings when working with large sets of data.

Here's what kind of speed-up you can expect when using SQL Server:

- **Insert:** 14x faster, reducing time by 93% (Online Benchmark)
- **Update:** 4x faster, reducing time by 75% (Online Benchmark)
- **Delete:** 3x faster, reducing time by 65% (Online Benchmark)

## Getting Started

In this tutorial, you will learn how to use bulk extension methods in the latest EF Core and earlier versions.

Unfortunately, EF Core doesn't provide any official API for bulk operations. To improve performance when working with bulk operations, you can use the [Entity Framework Extensions](https://entityframework-extensions.net/) library created by [ZZZ Projects](https://zzzprojects.com/).

### EF Core Bulk Extensions - Methods

| Name | Description |
| --- | --- |
| [Bulk Insert](/bulk-extensions/bulk-insert) | Extension method to insert entities in bulk. | 
| [Bulk Insert or Update](/bulk-extensions/bulk-merge) | Extension method to insert or update entities in bulk (Upsert operation). | 
| [Bulk Insert or Update or Delete](/bulk-extensions/bulk-synchronize) | Extension method to insert, update, or delete entities in bulk (Sync operation). | 
| [Bulk Update](/bulk-extensions/bulk-update) | Extension method to update entities in bulk. | 
| [Bulk Delete](/bulk-extensions/bulk-delete) | Extension method to delete entities in bulk. | 
| [Bulk Read](#ef-core-bulk-read-extensions) | Extension method to read entities in bulk. | 
| [Where Bulk Contains](/bulk-extensions/where-bulk-contains) | Extension method to perform a `WHERE` clause in bulk. | 
| [Bulk Save Changes](#ef-core-bulk-savechanges-extensions) | Extension method to save entities in bulk. |  

### NuGet - EF Core Bulk Extensions

To add Entity Framework Extensions, you need the following NuGet package:  
[https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/)

You must match the EF Core major version with the EF Extensions major version. For example:

- If you're using EF Core 9.x, use EF Extensions 9.x  
- If you're using EF Core 8.x, use EF Extensions 8.x  
- If you're using EF Core 7.x, use EF Extensions 7.x  
- If you're using EF Core 6.x, use EF Extensions 6.x  
- If you're using EF Core 5.x, use EF Extensions 5.x  

### GitHub - EF Core Bulk Extensions

In addition to the [official website](https://entityframework-extensions.net/), you can find other useful resources on GitHub:

- Project: [https://github.com/zzzprojects/EntityFramework-Extensions](https://github.com/zzzprojects/EntityFramework-Extensions)  
- Issues Tracker: [https://github.com/zzzprojects/EntityFramework-Extensions/issues](https://github.com/zzzprojects/EntityFramework-Extensions/issues)  
- Release Notes: [https://github.com/zzzprojects/EntityFramework-Extensions/releases](https://github.com/zzzprojects/EntityFramework-Extensions/releases) 

### EF Core Bulk Insert Extensions

The `BulkInsert` method improves performance compared to `SaveChanges` when you need to insert multiple entities.  
To perform a bulk insert, just call the method with the list of entities you want to insert.

```csharp
context.BulkInsert(customers);
```

Learn more in the [EF Core Bulk Insert Documentation](https://entityframework-extensions.net/bulk-insert)

### EF Core Bulk Insert or Update Extensions (Upsert)

The `BulkMerge` method improves performance compared to `SaveChanges` when you need to insert or update multiple entities.  
This is called an *upsert* operation — it updates existing entities and inserts new ones.  
To perform a bulk merge, just call the method with the list of entities you want to insert or update.

```csharp
context.BulkMerge(customers);
```

Learn more in the [EF Core Bulk Merge Documentation](https://entityframework-extensions.net/bulk-merge).

### EF Core Bulk Insert or Update or Delete Extensions (Sync)

The `BulkSynchronize` method improves performance compared to `SaveChanges` when you need to insert, update, or delete multiple entities.  
This is called a *sync* operation — it updates existing entities, inserts new ones, and deletes entities that are not present in your provided list.  
To perform a bulk synchronization, just call the method with the list of entities you want to keep (insert or update).  
Any entities not in the list will be deleted.

```csharp
context.BulkSynchronize(customers);
```

Learn more in the [EF Core Bulk Synchronize Documentation](https://entityframework-extensions.net/bulk-synchronize).

### EF Core Bulk Update Extensions

The `BulkUpdate` method improves performance compared to `SaveChanges` when you need to update multiple entities.  
To perform a bulk update, just call the method with the list of entities you want to update.

```csharp
context.BulkUpdate(customers);
```

Learn more in the [EF Core Bulk Update Documentation](https://entityframework-extensions.net/bulk-update).

---

### EF Core Bulk Delete Extensions

The `BulkDelete` method improves performance compared to `SaveChanges` when you need to delete multiple entities.  
To perform a bulk delete, just call the method with the list of entities you want to delete.

```csharp
context.BulkDelete(customers);
```

Learn more in the [EF Core Bulk Delete Documentation](https://entityframework-extensions.net/bulk-delete).

---

### EF Core Bulk Read Extensions

The `BulkRead` method allows you to read data from the database with more control than standard `Where` queries.  
For example, you can provide a list and specify which keys to use when retrieving data.

```csharp
context.Customers.BulkRead(list, x => new { x.FirstName, x.LastName });
```

Learn more in the [EF Core Bulk Read Documentation](https://entityframework-extensions.net/bulk-read).

---

### EF Core Bulk SaveChanges Extensions

The `BulkSaveChanges` method improves performance compared to `SaveChanges`.  
Even though `SaveChanges` is now much faster in EF Core, when you're working with thousands of entities, you can still gain major performance improvements by using `BulkSaveChanges`.  
To use it, just replace your call to `SaveChanges` with `BulkSaveChanges`.

```csharp
context.BulkSaveChanges();
```

Learn more in the [EF Core Bulk SaveChanges Documentation](https://entityframework-extensions.net/bulk-savechanges).

## Conclusion

The [Entity Framework Extensions](https://entityframework-extensions.net/) library is a great tool to improve the performance of your CRUD operations (Create, Read, Update, Delete).

In this article, we only covered the basic usage of each method, but you can learn more by visiting their respective documentation pages.