---
Title: Bulk Extensions for EF Core | Bulk Insert, Update, Delete, Merge & Upsert
MetaDescription: Learn how to use Bulk Extensions in EF Core to boost your CRUD operations performance. Discover how to save entities faster and by using less memory. Try it now.
LastMod: 2026-08-18
---

# Entity Framework Bulk Extensions

**Bulk Extensions** are high-performance extension methods added to **EF Core** by Entity Framework Extensions. They are designed to **speed up your CRUD operations** and **dramatically reduce memory usage** in your application. These bulk methods are both **easy to use** and **easy to customize**.

Here’s an example of how to [bulk insert in EF Core](https://entityframework-extensions.net/bulk-insert) a list of `Invoice` entities along with their related `InvoiceItems`, using the [IncludeGraph](https://entityframework-extensions.net/include-graph) option:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(invoices, options => { options.IncludeGraph = true; });
```

The [BulkInsert](https://entityframework-extensions.net/bulk-insert) method from Entity Framework Extensions is not only much faster than using `SaveChanges`, it also [reduces memory consumption](https://entityframework-extensions.net/v7-100-0-0-include-graph#memory-performance-improvements).

## Why Choose Entity Framework Extensions for Bulk Operations?

Performance is only part of the value. Entity Framework Extensions gives your team a production-ready solution backed by years of development, extensive testing, and expert support.

- **Build faster with less risk:** Replace weeks of development, testing, and provider-specific code with powerful bulk operations that work in only a few lines of code.
- **Proven reliability:** Before every release, we run **more than 20,000 unit tests** to detect regressions and ensure existing features continue to work as expected.
- **Trusted in production:** Since 2014, **more than 5,000 companies** have trusted Entity Framework Extensions for their applications.
- **Tier-1 customer support:** Get direct help from experienced Entity Framework experts, often with a personalized [online example](/online-examples). We also proactively follow up to make sure your issue is fully resolved.
- **Ready for what comes next:** Benefit from **day-one support for every major EF Core release** and reliable monthly updates with fixes, improvements, and new features.

You are not simply adding bulk methods to your application. You are benefiting from **more than a decade of development, testing, and expertise that your team does not have to rebuild**.

[Discover the value behind Entity Framework Extensions →](/why-entity-framework-extensions)

## 🚀 Performance Benchmark

Let’s compare the performance of Entity Framework Extensions to EF Core `SaveChanges` method:

**10k Invoices, 50k InvoiceItems**:

|             | IncludeGraph | SaveChanges |
| ----------- | :----------: | :---------: |
| Performance |     1.5s     |      6s     |
| Memory      |     60 MB    |    200 MB   |

**100k Invoices, 500k InvoiceItems**:

|             | IncludeGraph | SaveChanges |
| ----------- | :----------: | :---------: |
| Performance |      10s     |     58s     |
| Memory      |    400 MB    |   1800 MB   |

With Entity Framework Extensions, you can boost performance by more than **5x** and reduce memory usage to just **20%** of what `SaveChanges` consumes.

### 📊 Benchmark Results

The tables above give you a quick idea of the performance gains when using **Bulk Insert**.  
But to give you a more complete picture, we also ran **extensive benchmarks across all major database providers** with BenchmarkDotNet.

👉 Explore detailed results:  

- By provider (EF Core):
   - [SQL Server](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlserver.md)
   - [PostgreSQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-postgresql.md)
   - [MySQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mysql.md)
   - [MariaDB](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mariadb.md)
   - [Oracle](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-oracle.md)
   - [SQLite](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlite.md)  
- By operation (EF Core):
   - [Bulk Insert](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-insert.md)
   - [Bulk Update](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-update.md)
   - [Bulk Delete](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-delete.md)
   - [Bulk Merge](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-merge.md)
   - [Bulk SaveChanges](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-savechanges.md)
   - [Bulk Synchronize](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-synchronize.md)  

Here’s an example chart for **SQL Server – Bulk Insert** comparing **EF Core SaveChanges** vs **EF Extensions BulkInsert**:

![Benchmark EF Core vs EF Extensions – SQL Server - Bulk Insert](https://raw.githubusercontent.com/zzzprojects/EntityFramework-Extensions/master/images/benchmark-efcore-vs-efe-sqlserver-bulk-insert.png)

## Which Bulk Methods Does Entity Framework Extensions Support?

Whether you need to **save** or **query** data in bulk, Entity Framework Extensions provides a complete set of high-performance bulk methods to match your needs:

* **Saving**

  * [BulkInsert](/bulk-insert) — Insert thousands of entities quickly and efficiently.
  * [BulkUpdate](/bulk-update) — Update large sets of entities without having to retrieve them first.
  * [BulkDelete](/bulk-delete) — Delete many entities without the performance hit of `SaveChanges`.
  * [BulkMerge](/bulk-merge) — Insert or update in one smart operation (also known as upsert).
  * [BulkSynchronize](/bulk-synchronize) — Sync your list with the database by adding, updating, and deleting in one step.
  * [BulkSaveChanges](/bulk-savechanges) — Save all pending changes in one high-performance batch.

* **Querying**

  * [BulkRead](/bulk-read) — Efficiently read a large number of entities from the database.
  * [WhereBulkContains](/where-bulk-contains) — Filter a database table to return only rows that match the entities in your list.
  * [WhereBulkNotContains](/where-bulk-not-contains) — Filter a database table to return only rows that don’t match the entities in your list.
  * [WhereBulkContainsFilterList](/where-bulk-contains-filter-list) — Filter your in-memory list to return only items that match rows in the database.
  * [WhereBulkNotContainsFilterList](/where-bulk-not-contains-filter-list) — Filter your in-memory list to return only items that don’t match rows in the database.


## Which Versions of Entity Framework Are Supported?

In short, **all versions of EF Core and Entity Framework are supported**:

* **Entity Framework Core:** EF Core 10 and earlier
* **Entity Framework:** EF6 and earlier

## Which Providers Are Supported by Entity Framework Extensions?

Entity Framework Extensions supports all major providers available in EF Core, including:

* **SQL Server**
* **MySQL**
* **MariaDB**
* **Oracle**
* **PostgreSQL**
* **SQLite**

## Bulk Insert — For EF Core with Entity Framework Extensions

The [**EF Core `BulkInsert`**](/bulk-insert) method from Entity Framework Extensions is one of the core features of the library. It allows you to insert **thousands or even millions of entities** into your database **quickly and efficiently**.

### 🔧 Commonly Used Options

* **AutoMapOutputDirection** — Improves performance by skipping the return of output values like identity columns.
* **InsertIfNotExists** — Inserts only the entities that don't already exist in the database.
* **InsertKeepIdentity** — Allows inserting custom identity values instead of letting the database generate them.
* **IncludeGraph** — Automatically inserts related entities found in the object graph and preserves their relationships.

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Simple bulk insert
context.BulkInsert(customers);

// Bulk insert with options
context.BulkInsert(customers, options =>
{
    options.AutoMapOutputDirection = false;
    options.InsertIfNotExists = true;
});

// Bulk insert from anonymous list
context.BulkInsert<Customer>(anonymousList);

// Bulk insert async version
await context.BulkInsertAsync(customers);
```

## Bulk Update — For EF Core with Entity Framework Extensions

The [**EF Core `BulkUpdate`**](/bulk-update) method from Entity Framework Extensions allows you to **update large sets of data** in your database efficiently and with full control.

### 🔧 Commonly Used Options

* **ColumnPrimaryKeyExpression** — Use a custom key expression to identify which entities should be updated.
* **ColumnInputExpression** — Specify which columns to update using a LINQ expression.
* **ColumnInputNames** — Specify which columns to update by listing their names as strings.
* **IncludeGraph** — Automatically updates related entities found in the object graph, preserving relationships.

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Simple bulk update
context.BulkUpdate(customers);

// Bulk update specific columns using an expression
context.BulkUpdate(customers, options =>
{
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
});

// Bulk update from an anonymous list
context.BulkUpdate<Customer>(anonymousList);

// Bulk update async version
await context.BulkUpdateAsync(customers);
```

## Bulk Delete — For EF Core with Entity Framework Extensions

The [**EF Core `BulkDelete`**](/bulk-delete) method from Entity Framework Extensions allows you to **delete large sets of data** from your database efficiently in one operation.

### 🔧 Commonly Used Options

* **ColumnPrimaryKeyExpression** — Use a custom key to identify which entities should be deleted.
* **DeleteMatchedAndConditionExpression** — Delete only when all specified values from the source and destination match.
* **DeleteMatchedAndOneNotConditionExpression** — Delete only when at least one specified value from the source differs from the destination.
* **DeleteMatchedAndFormula** — Apply a SQL condition to decide whether or not to delete each entity.

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Simple bulk delete
context.BulkDelete(customers);

// Bulk delete with a custom SQL condition
context.BulkDelete(customers, options =>
{
    options.DeleteMatchedAndFormula = "DestinationTable.CreatedDate < DATEADD(YEAR, -2, GETDATE())";
});

// Bulk delete from an anonymous list
context.BulkDelete<Customer>(anonymousList);

// Bulk delete async version
await context.BulkDeleteAsync(customers);
```

## Bulk Merge / Upsert — For EF Core with Entity Framework Extensions

The [**EF Core `BulkMerge`**](/bulk-merge) method from Entity Framework Extensions lets you **insert or update data in bulk** — depending on whether the record already exists. This feature is often referred to as `Upsert`, `AddOrUpdate`, or `InsertOrUpdate`.

### 🔧 Commonly Used Options

* **ColumnPrimaryKeyExpression** — Use a custom key expression to determine if an entity already exists in the database.
* **IgnoreOnMergeInsertExpression** — Exclude specific columns during the **insert** phase of the merge operation.
* **IgnoreOnMergeUpdateExpression** — Exclude specific columns during the **update** phase of the merge operation.
* **IncludeGraph** — Automatically handles related entities in the object graph and preserves data relationships.

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Simple bulk merge (upsert)
context.BulkMerge(customers);

// Bulk merge with options
context.BulkMerge(customers, options =>
{
    options.ColumnPrimaryKeyExpression = x => new { x.Code };
    options.IgnoreOnMergeInsertExpression = x => new { x.UpdatedDate, x.UpdatedBy };
    options.IgnoreOnMergeUpdateExpression = x => new { x.CreatedDate, x.CreatedBy };
});

// Bulk merge from an anonymous list
context.BulkMerge<Customer>(anonymousList);

// Bulk merge async version
await context.BulkMergeAsync(customers);
```

## Bulk Synchronize — For EF Core with Entity Framework Extensions

The [**EF Core `BulkSynchronize`**](/bulk-synchronize) method from Entity Framework Extensions allows you to **insert**, **update**, or **delete** data in your database in bulk — all in one operation. In other words, your database table becomes a **mirror** of the list of entities you provide.

This feature is often searched for under names like `AddOrUpdateOrDelete` or `InsertOrUpdateOrDelete`.

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Simple bulk synchronize (upsert)
context.BulkSynchronize(customers);

// Bulk synchronize with options
context.BulkSynchronize(customers, options => { /* custom settings here */ });

// Bulk synchronize from an anonymous list
context.BulkSynchronize<Customer>(anonymousList);

// Bulk synchronize async version
await context.BulkSynchronizeAsync(customers);
```

## Bulk SaveChanges — For EF Core with Entity Framework Extensions

The [**EF Core `BulkSaveChanges`**](/bulk-savechanges) method from Entity Framework Extensions is a high-performance replacement for the standard `SaveChanges` method. It processes **inserts**, **updates**, and **deletes** — just like `SaveChanges` — but runs them **much faster**.

The only thing you need to change? Replace `SaveChanges` with `BulkSaveChanges` — and you're done!

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Standard EF Core
// context.SaveChanges();

// Improved performance with BulkSaveChanges
context.BulkSaveChanges();

// BulkSaveChanges with custom options
context.BulkSaveChanges(options => { /* custom settings */ });

// BulkSaveChanges async version
await context.BulkSaveChangesAsync();
```

## Bulk Read — For EF Core with Entity Framework Extensions

The [**EF Core `BulkRead`**](/bulk-read) method from Entity Framework Extensions allows you to efficiently retrieve entities from your database by:

* Providing a list of `IDs` (e.g., `int`, `Guid`, `string`, etc.)
* Providing a list of entities and using their primary key or a custom key expression

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Bulk read by list of IDs
var list1 = context.Customers.BulkRead(ids);

// Bulk read by custom key
var list2 = context.Customers.BulkRead(list, x => new { x.Email, x.PhoneNumber });

// Bulk read async versions
var list3 = await context.Customers.BulkReadAsync(ids);
```

## Where Bulk Contains — For EF Core with Entity Framework Extensions

The [**EF Core `WhereBulkContains`**](/where-bulk-contains) method from Entity Framework Extensions allows you to **filter your database table** to return only the entities that match the values in a list.

It supports **almost any kind of data source**, including:

* Basic types like `List<int>` or `List<Guid>`
* Entity types like `List<Customer>`
* Anonymous types
* Lists of `ExpandoObject`

Unlike `BulkRead`, the `WhereBulkContains` method is **deferred**, meaning it doesn't execute immediately. It simply builds the query.
If you want to retrieve the results, just call `.ToList()` (which is what `BulkRead` does under the hood).

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Filter by list of IDs
var list1 = context.Customers.WhereBulkContains(ids).ToList();

// Filter by custom key
var list2 = context.Customers.WhereBulkContains(list, x => new { x.Email, x.PhoneNumber });

// Async version
var list3 = await context.Customers.WhereBulkContains(ids).ToListAsync();
```

## Where Bulk Not Contains — For EF Core with Entity Framework Extensions

The [**EF Core `WhereBulkNotContains`**](/where-bulk-not-contains) method from Entity Framework Extensions works just like `WhereBulkContains`, but in reverse.
It returns all entities **from the database** that **do not match** the values in the provided list.

You can use it with:

* Basic types like `List<int>` or `List<Guid>`
* Entity types like `List<Customer>`
* Anonymous types
* Lists of `ExpandoObject`

Like `WhereBulkContains`, `WhereBulkNotContains` is a **deferred method** — it builds the query but doesn't execute it until you call something like `.ToList()`.
In contrast, `BulkRead` is an immediate method that internally uses `WhereBulkContains().ToList()`.

### 💡 Example Usage

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Filter to get database records NOT in the list of IDs
var list1 = context.Customers.WhereBulkNotContains(ids).ToList();

// Filter by custom key
var list2 = context.Customers.WhereBulkNotContains(list, x => new { x.Email, x.PhoneNumber });

// Async version
var list3 = await context.Customers.WhereBulkNotContains(ids).ToListAsync();
```

## Conclusion

Using Bulk Extensions in EF Core with Entity Framework Extensions is one of the most effective ways to boost the **performance** and **memory efficiency** of your application — especially in its most critical areas.

Whether you're inserting, updating, deleting, or reading large amounts of data, these methods are built to do it **fast** and **reliably**. Plus, with **hundreds of customization options**, you can tailor each bulk operation to fit your exact needs.

Every developer working with EF Core should [**try Entity Framework Extensions**](https://entityframework-extensions.net/download) at least once — you'll instantly see the difference.
