---
Title: Entity Framework Bulk Operations for EF Core & EF6 (95% Faster)
MetaDescription: Learn how EF Extensions adds bulk insert, update, delete, merge, and sync for EF Core & EF6—cut SaveChanges time by up to 95%.
LastMod: 2025-12-19
---

# Entity Framework Bulk Operations

Entity Framework Extensions (EF Extensions) extends EF Core and EF6 by adding bulk operations.

Bulk operations let you **insert, update, delete, merge, or synchronize many rows** in the database **much faster** than `SaveChanges`.

There are **two main reasons** why you would want to use bulk operations instead of `SaveChanges`:

* [For performance](#bulk-operations-performance): Reduce saving time by **up to 95%** with real bulk operations
* [For flexibility](#bulk-operations-flexibility): **Hundreds of options** to fully control how data is saved

These reasons are why EF Extensions provides multiple bulk operations, each designed for a specific scenario.

There are **6 bulk operation methods** available:

* [BulkInsert](/bulk-insert)
* [BulkInsertOptimized](/bulk-insert-optimized)
* [BulkUpdate](/bulk-update)
* [BulkDelete](/bulk-delete)
* [BulkMerge](/bulk-merge) (insert + update operation)
* [BulkSynchronize](/bulk-synchronize) (insert + update + delete operation)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsert(list);
context.BulkInsertOptimized(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);
context.BulkSynchronize(list);

// Easy to customize
context.BulkMerge(customers, bulk =>
{
    bulk.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```

## Bulk Operations Performance

With **Entity Framework Extensions**, the performance gain depends on how many entities you save.

When saving only a few entities, the performance boost is usually not noticeable.

However, as soon as you start saving more entities, the benefits of bulk operations become very clear.

The more entities you save, the more `SaveChanges` becomes expensive, while **EF Extensions bulk operations** scale much better.

For example, with **EF Core and SQL Server**:

* Insert: **18x faster**, reducing execution time by **95%** ([Online Benchmark](https://dotnetfiddle.net/cFWgKV))
* Update: **4x faster**, reducing execution time by **75%** ([Online Benchmark](https://dotnetfiddle.net/ope4nq))
* Delete: **3x faster**, reducing execution time by **65%** ([Online Benchmark](https://dotnetfiddle.net/zzMQgZ))

| Operation                          | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :--------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                        |         325 ms |         575 ms |       1,400 ms |
| BulkInsert (Outputting values)     |          60 ms |          90 ms |         150 ms |
| BulkInsert (Not Outputting values) |          30 ms |          50 ms |          90 ms |
| BulkInsertOptimized                |          30 ms |          50 ms |          90 ms |

You can try these scenarios online by clicking on their corresponding **Online Benchmark** links.

You can also find additional benchmark results here:

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

For **EF6**, the performance difference is even more significant. EF6 performs a database round-trip for each entity being saved, while EF Core improved this behavior.

Because of this, **EF Extensions bulk operations** usually provide even bigger gains when using EF6.

## Bulk Operations Flexibility

**Entity Framework Extensions** is the most feature-rich libraries, providing **hundreds of options** so you can adapt bulk operations to your data model and business rules.

You can customize **which columns are used**, for example:

* [Primary Key](/primary-key): Choose a custom key when saving entities
* [Input / Output / Ignore](/input-output-ignore): Choose which properties are saved or ignored
* [Coalesce](/coalesce): Control how database `NULL` values should behave

You can also control **how the bulk operation itself behaves**:

* [Audit](/audit): Track all changes made to your database during a bulk operation
* [Include Graph](/include-graph): Include related child entities in the save operation
* [Logging](/logging): Capture and analyze SQL statements, parameters, and execution details
* [Temporary Table](/temporary-table): Control how temporary tables are created and used

This is only a **small subset** of all available options.

```csharp
// Include related entities
context.BulkMerge(customers, options =>
{
	options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
    options.IncludeGraph = true;
});

// Enable auditing
List<AuditEntry> auditEntries = new List<AuditEntry>();

context.BulkMerge(list, options =>
{
    options.UseAudit = true;
    options.AuditEntries = auditEntries;
});
```

## Summary & Next Steps

**Entity Framework Extensions bulk operations** let you save large amounts of data **much faster** than `SaveChanges`, while giving you **full control** over how data is written to the database.

Bulk operations are a good choice when you save **hundreds or thousands of entities**, when performance matters, or when you need more control than what `SaveChanges` offers.

Each bulk method targets a specific scenario and can be customized with many options to match your data model and business rules.

To go further, the next step is to learn how to configure bulk operations in detail:

* **[Configure Options](/configure-options)** — Learn how to configure options using lambdas, option instances, and global settings.
* **[Configure Column Options](/configure-column-options)** — Learn how to control which columns are used, ignored, or customized during bulk operations.