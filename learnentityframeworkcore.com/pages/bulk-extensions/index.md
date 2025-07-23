---
title: EF Extensions: Bulk Insert, Update, Delete, Read, SaveChanges
description: Learn to use Bulk Extensions methods from Entity Framework Extensions in EF Core. Improve your .NET Core performance with bulk methods such as BulkInsert, BulkUpdate, BulkDelete, BulkRead, BulkSaveChanges.
canonical: /bulk-extensions
status: Published
lastmod: 2025-07-23
---

# Bulk Extensions with EF Core from Entity Framework Extensions

Entity Framework Core (EF Core) is well-known as a great ORM (Object-Relational Mapping) tool. It makes saving entities to your database super easy—without writing any SQL statements.

But when you need to handle thousands of entities, EF Core’s performance can quickly become a bottleneck. That’s where the **Bulk Extensions Methods** from the [Entity Framework Extensions](https://entityframework-extensions.net/) library come in. This powerful library helps dramatically boost the performance of EF Core.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsert(customers);
context.BulkUpdate(customers);
context.BulkDelete(customers);
context.BulkMerge(customers);
context.BulkSynchronize(customers);

// Easy to customize
context.BulkInsert(customers, options => options.IncludeGraph = true);
```

### Why Use Bulk Extensions from Entity Framework Extensions?

Using [Bulk Extensions from Entity Framework Extensions](https://entityframework-extensions.net/bulk-extensions) library comes with a lot of advantages:

* ✅ **Easy to use** – The syntax is simple and intuitive
* ✅ **Easy to customize** – Over 100 options available to fit your needs
* ✅ **Supports all EF Core versions**
* ✅ **Supports all kinds of models** – Inheritance, complex types, owned entity types, value converters, and more
* ✅ **Database agnostic** – Works with SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle


## 📦 How to Install Entity Framework Extensions

To use the `Bulk Extensions methods`, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Make sure the **first digit** of the version matches your EF Core version.
For example, if you're using `EF Core 9`, use version `v9.x.y.z` of the package.

Learn more about [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning).

Need another version like EF6? Visit the official downloads page:
👉 [https://entityframework-extensions.net/download](https://entityframework-extensions.net/download)

### Performance

One of the biggest advantages of using [Bulk Extensions from Entity Framework Extensions](https://entityframework-extensions.net/bulk-extensions) library is the massive performance improvement it brings when working with large sets of data.

Here's what kind of speed-up you can expect when using SQL Server:

- **Insert:** 14x faster, reducing time by 93% ([Online Benchmark](https://dotnetfiddle.net/cFWgKV))
- **Update:** 4x faster, reducing time by 75% ([Online Benchmark](https://dotnetfiddle.net/ope4nq))
- **Delete:** 3x faster, reducing time by 65% ([Online Benchmark](https://dotnetfiddle.net/zzMQgZ))

## Getting Started

In this tutorial, you will learn how to use [Bulk Extensions from Entity Framework Extensions](https://entityframework-extensions.net/bulk-extensions) library in the latest EF Core and earlier versions.

Unfortunately, EF Core doesn't provide any official API for bulk operations. To improve performance when working with bulk operations, you can use the [Entity Framework Extensions](https://entityframework-extensions.net/) library created by [ZZZ Projects](https://zzzprojects.com/).

### EF Core Bulk Extensions - Methods

Here is a list of some bulk extension methods offered by Entity Framework Extensions:

| Name                                                                 | Description                                                            |
| -------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| [Bulk Insert](/bulk-extensions/bulk-insert)                          | Insert entities in bulk.                                               |
| [Bulk Insert or Update](/bulk-extensions/bulk-merge)                 | Insert or update entities in bulk (also known as an Upsert operation). |
| [Bulk Insert or Update or Delete](/bulk-extensions/bulk-synchronize) | Insert, update, or delete entities in bulk (Sync operation).           |
| [Bulk Update](/bulk-extensions/bulk-update)                          | Update entities in bulk.                                               |
| [Bulk Delete](/bulk-extensions/bulk-delete)                          | Delete entities in bulk.                                               |
| [Bulk Read](#ef-core-bulk-read-extensions)                           | Read entities in bulk.                                                 |
| [Where Bulk Contains](/bulk-extensions/where-bulk-contains)          | Perform a `WHERE` clause in bulk.                                      |
| [Bulk Save Changes](#ef-core-bulk-savechanges-extensions)            | Save changes for multiple entities in bulk.                            |

### GitHub - EF Core Bulk Extensions

In addition to the [official website](https://entityframework-extensions.net/), you can find other useful resources on GitHub:

- Project: [https://github.com/zzzprojects/EntityFramework-Extensions](https://github.com/zzzprojects/EntityFramework-Extensions)  
- Issues Tracker: [https://github.com/zzzprojects/EntityFramework-Extensions/issues](https://github.com/zzzprojects/EntityFramework-Extensions/issues)  
- Release Notes: [https://github.com/zzzprojects/EntityFramework-Extensions/releases](https://github.com/zzzprojects/EntityFramework-Extensions/releases) 

### EF Core Bulk Insert Extensions

The [BulkInsert](/bulk-extensions/bulk-insert) method from Entity Framework Extensions improves performance compared to `SaveChanges` when you need to insert multiple entities.  

To perform a bulk insert, just call the method with the list of entities you want to insert.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore

context.BulkInsert(customers);
```

Learn more in the [EF Core Bulk Insert Documentation](https://entityframework-extensions.net/bulk-insert)

### EF Core Bulk Insert or Update Extensions (Upsert)

The [BulkMerge](/bulk-extensions/bulk-merge) method from Entity Framework Extensions improves performance compared to `SaveChanges` when you need to insert or update multiple entities.  
This is called an *upsert* operation — it updates existing entities and inserts new ones.  
To perform a bulk merge, just call the method with the list of entities you want to insert or update.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers);
```

Learn more in the [EF Core Bulk Merge Documentation](https://entityframework-extensions.net/bulk-merge).

### EF Core Bulk Insert or Update or Delete Extensions (Sync)

The [BulkSynchronize](/bulk-extensions/bulk-synchronize) method from Entity Framework Extensions improves performance compared to `SaveChanges` when you need to insert, update, or delete multiple entities.  
This is called a *sync* operation — it updates existing entities, inserts new ones, and deletes entities that are not present in your provided list.  
To perform a bulk synchronization, just call the method with the list of entities you want to keep (insert or update).  
Any entities not in the list will be deleted.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers);
```

Learn more in the [EF Core Bulk Synchronize Documentation](https://entityframework-extensions.net/bulk-synchronize).

### EF Core Bulk Update Extensions

The [BulkUpdate](/bulk-extensions/bulk-update) method from Entity Framework Extensions improves performance compared to `SaveChanges` when you need to update multiple entities.  
To perform a bulk update, just call the method with the list of entities you want to update.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkUpdate(customers);
```

Learn more in the [EF Core Bulk Update Documentation](https://entityframework-extensions.net/bulk-update).

---

### EF Core Bulk Delete Extensions


The [BulkDelete](/bulk-extensions/bulk-delete) method from Entity Framework Extensions improves performance compared to `SaveChanges` when you need to delete multiple entities.  
To perform a bulk delete, just call the method with the list of entities you want to delete.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(customers);
```

Learn more in the [EF Core Bulk Delete Documentation](https://entityframework-extensions.net/bulk-delete).

---

### EF Core Bulk Read Extensions

The [BulkRead](https://entityframework-extensions.net/bulk-read) method from Entity Framework Extensions allows you to read data from the database with more control than standard `Where` queries.  
For example, you can provide a list and specify which keys to use when retrieving data.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.BulkRead(list, x => new { x.FirstName, x.LastName });
```

Learn more in the [EF Core Bulk Read Documentation](https://entityframework-extensions.net/bulk-read).

---

### EF Core Bulk SaveChanges Extensions
https://entityframework-extensions.net/bulk-savechanges

The `BulkSaveChanges` method improves performance compared to `SaveChanges`.  
Even though `SaveChanges` is now much faster in EF Core, when you're working with thousands of entities, you can still gain major performance improvements by using `BulkSaveChanges`.  
To use it, just replace your call to `SaveChanges` with `BulkSaveChanges`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSaveChanges();
```

Learn more in the [EF Core Bulk SaveChanges Documentation](https://entityframework-extensions.net/bulk-savechanges).

## Conclusion

The [Entity Framework Extensions](https://entityframework-extensions.net/) library is a great tool to improve the performance of your CRUD operations (Create, Read, Update, Delete).

In this article, we only covered the basic usage of each method, but you can learn more by visiting their respective documentation pages.