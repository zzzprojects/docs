---
Title: Getting Started with Entity Framework Extensions (EF Core & EF6)
MetaDescription: Learn how to get started with Entity Framework Extensions. Install, configure, and use bulk operations and BulkSaveChanges with EF Core & EF6.
LastMod: 2025-12-17
---

# EF Extensions - Overview

## What is Entity Framework Extensions?

**Entity Framework Extensions** is a library that dramatically improves EF Core and EF6 saving performance by using bulk operations.

For example, with **EF Core and SQL Server**:

* Insert: **18x faster**, reducing execution time by **95%** ([Online Benchmark](https://dotnetfiddle.net/cFWgKV))
* Update: **4x faster**, reducing execution time by **75%** ([Online Benchmark](https://dotnetfiddle.net/ope4nq))
* Delete: **3x faster**, reducing execution time by **65%** ([Online Benchmark](https://dotnetfiddle.net/zzMQgZ))

👉 See **[detailed benchmarks](#benchmark-results)** by database provider and by bulk operation.

It is easy to use and easy to customize.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);
context.BulkSaveChanges();

// Easy to customize
context.BulkMerge(customers, options =>
    options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## Supported EF versions and databases

### EF Core versions

* EF Core 10
* EF Core 9
* EF Core 8
* EF Core 7
* EF Core 6
* EF Core 5
* EF Core 3
* EF Core 2

### Entity Framework versions

* Entity Framework 6
* Entity Framework 5
* Entity Framework 4

### Database providers

* SQL Server 2008+
* SQL Azure
* SQL Compact
* Oracle
* MySQL
* MariaDB
* PostgreSQL
* SQLite

## How to install EF Extensions?

Install EF Extensions from **[NuGet](/download)**.

Choose the package that matches your EF version (EF Core, EF6, EF5, or EF4), then install it using NuGet or the .NET CLI.

Once installed, bulk extension methods are available directly on your `DbContext`.

## EF Extensions - Bulk Operations

Entity Framework Extensions **bulk operations improve performance** by executing operations directly in the database.

They also give you more control when saving data. You can customize options such as:

* Primary keys
* Columns to insert or update
* Include graph (child entities)
* Auditing
* Logging
* And more

Bulk operations are faster than `SaveChanges` and `BulkSaveChanges` because they:

* Do not use the Change Tracker
* Do not call the `DetectChanges` method

**Available bulk operations:**

* [BulkInsert](/bulk-insert)
* [BulkUpdate](/bulk-update)
* [BulkDelete](/bulk-delete)
* [BulkMerge](/bulk-merge) (UPSERT operation)
* [BulkSynchronize](/bulk-synchronize)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options =>
{
    options.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```

👉 Learn more: **[Getting Started with Bulk Operations](/tutorial-bulk-operations)**

## EF Extensions - BulkSaveChanges

The `BulkSaveChanges` method is an **upgraded version of `SaveChanges`**.
It works exactly like `SaveChanges`, but faster.

It executes all pending changes from the `ChangeTracker` using **bulk operations**, dramatically improving performance while keeping the same behavior you are used to.

`BulkSaveChanges` supports everything that `SaveChanges` supports:

* Associations (one-to-one, one-to-many, many-to-many, etc.)
* Owned types
* Complex types
* Enums
* Inheritance (TPC, TPH, TPT)
* Navigation properties
* Self-hierarchy
* And more

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.AddRange(listToAdd); // add
context.Customers.RemoveRange(listToRemove); // remove
listToModify.ForEach(x => x.DateModified = DateTime.Now); // modify

// Easy to use
context.BulkSaveChanges();

// Easy to customize
context.BulkSaveChanges(options => options.BatchSize = 100);
```

👉 Learn more: **[Getting Started with BulkSaveChanges](/tutorial-bulk-savechanges)**

## EF Extensions - Batch Operations

Batch operations allow you to perform **INSERT**, **UPDATE**, and **DELETE** operations **directly in the database** using a LINQ query, without loading entities into the context.

Everything is executed on the database side to give you the **best performance possible**.

**Available batch operations:**

* [InsertFromQuery](/insert-from-query)
* [UpdateFromQuery](/update-from-query)
* [DeleteFromQuery](/delete-from-query)

The `UpdateFromQuery` and `DeleteFromQuery` methods are similar to EF Core
[`ExecuteUpdate` and `ExecuteDelete`](https://learn.microsoft.com/en-us/ef/core/saving/execute-insert-update-delete), but they use a **different syntax** and have been available long before these EF Core features were introduced.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT all customers inactive for more than two years into a backup table
var date = DateTime.Now.AddYears(-2);
context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .InsertFromQuery("bck_Customer", x => new { x.CustomerID, x.Name, x.Email });

// UPDATE all customers inactive for more than two years
context.Customers
    .Where(x => x.IsActive && x.LastLogin < DateTime.Now.AddYears(-2))
    .UpdateFromQuery(x => new Customer { IsActive = false });

// DELETE all customers inactive for more than two years
context.Customers
    .Where(x => x.LastLogin < DateTime.Now.AddYears(-2))
    .DeleteFromQuery();
```

👉 Learn more: **[Getting Started with Batch Operations](/tutorial-batch-operations)**

## Benchmark Results

You can explore **real benchmark results** that compare Entity Framework Extensions with standard EF Core behavior.
All benchmarks are reproducible and executed using realistic scenarios.

Results are available **by database provider** and **by bulk operation** to help you evaluate performance for your own environment.

* By provider (EF Core):

  * [SQL Server](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlserver.md)
  * [PostgreSQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-postgresql.md)
  * [MySQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mysql.md)
  * [MariaDB](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mariadb.md)
  * [Oracle](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-oracle.md)
  * [SQLite](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlite.md)

* By operation (EF Core):

  * [Bulk Insert](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-insert.md)
  * [Bulk Update](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-update.md)
  * [Bulk Delete](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-delete.md)
  * [Bulk Merge](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-merge.md)
  * [Bulk SaveChanges](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-savechanges.md)
  * [Bulk Synchronize](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-synchronize.md)

## Summary & Next Steps

Entity Framework Extensions gives you **multiple ways to dramatically improve performance**, depending on how you work with Entity Framework.

* Use **Bulk Operations** when you want maximum performance and full control.
* Use **BulkSaveChanges** when you want a fast, drop-in replacement for `SaveChanges`.
* Use **Batch Operations** when you want to insert, update or delete data directly from a LINQ query, without loading entities.

**Next steps:**

* Start with **[Getting Started with Bulk Operations](/tutorial-bulk-operations)** if you need more control and advanced scenarios.
* Explore **[Getting Started with BulkSaveChanges](/tutorial-bulk-savechanges)** if you want the easiest performance improvement.
* Learn more about **[Getting Started with Batch Operations](/tutorial-batch-operations)** for set-based insert, updates and deletes.
* Review the **[benchmarks](/benchmarks)** to see real performance numbers for your database provider.

From there, you can progressively optimize only the parts of your application that need it most.