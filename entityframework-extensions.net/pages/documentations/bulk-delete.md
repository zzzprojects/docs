---
Title: Bulk Delete in EF Core with Entity Framework Extensions
MetaDescription: Efficiently delete Entity Framework data with EF Core Bulk Delete Extensions. Customize options to quickly delete large numbers of entities with ease, compatible with all EF versions including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2026-07-14
---

# EF Core Bulk Delete with Entity Framework Extensions

The `BulkDelete` method lets you delete thousands of entities quickly and efficiently in EF Core and EF6.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.BulkDeleteAsync(customers);	

// Easy to customize
await context.BulkDeleteAsync(customers, options => options.IncludeGraph = true);
````

[Online Example](https://dotnetfiddle.net/BCyXU6)

Our library also offers other ways to delete your entities, depending on your scenario:

* [Delete from Query](/delete-from-query)
* [Delete by Key](/delete-by-key)
* [Delete Range by Key](/delete-range-by-key)

## Bulk Delete Example

### Delete with a Custom Key

The [ColumnPrimaryKeyExpression](/primary-key#using-columnprimarykeyexpression) and [ColumnPrimaryKeyNames](/primary-key#using-columnprimarykeynames) options let you delete entities by using a custom key (or a combination of properties) instead of your entity's mapped primary key.

This is particularly useful when your data comes from an external system and your entity's primary key is not available.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using `ColumnPrimaryKeyExpression`
context.BulkDelete(customers, 
    options => options.ColumnPrimaryKeyExpression = x => x.Code);	

// Using `ColumnPrimaryKeyNames`
var customKeys = new List<string>() { nameof(Customer.Code) };
context.BulkDelete(customers,
    options => options.ColumnPrimaryKeyNames = customKeys);
````

[Online Example](https://dotnetfiddle.net/91wZzc)

### Delete with Related Entities (Include Graph)

Use this option when you want to delete entities and automatically delete all related entities (children, grandchildren, and more) linked through navigation properties.

* [IncludeGraph](/include-graph): Automatically deletes all related entities linked through navigation properties.
* [IncludeGraphOperationBuilder](/include-graph#includegraphoperationbuilder): Lets you customize how a specific entity type is deleted.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(invoices, options => options.IncludeGraph = true);
```

[Online Example](https://dotnetfiddle.net/SHM63t)

**Note:** This option is only supported in EF Core 3+.

### Delete Only When Additional Conditions Match

The [DeleteMatchedAndConditionExpression](/delete-matched-and-condition#using-deletematchedandconditionexpression) and [DeleteMatchedAndConditionNames](/delete-matched-and-condition#using-deletematchedandconditionnames) options let you delete entities only when additional property values match between your entities and the database.

This is useful when you want extra safety and delete rows only if they still meet specific conditions.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using `DeleteMatchedAndConditionExpression`
context.BulkDelete(customers, options =>
    options.DeleteMatchedAndConditionExpression = x => x.Status);	

// Using `DeleteMatchedAndConditionNames`
var matchedConditions = new List<string>() { nameof(Customer.Status) };
context.BulkDelete(customers, options =>
    options.DeleteMatchedAndConditionNames = matchedConditions);
```

[Online Example](https://dotnetfiddle.net/QJOlyk)

### Delete with Future Action

Use this option when you want to delete entities later instead of executing the operation immediately.

By default, `BulkDelete` executes as soon as you call the method.

With [future actions](https://entityframework-plus.net/future-action), you can queue multiple bulk operations and execute them all at once later.

* `FutureAction`: Adds a `BulkDelete` operation to the pending action queue instead of executing it immediately.
* `ExecuteFutureAction`: Executes all pending future actions.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.FutureAction(x => x.BulkDelete(customers1));
context.FutureAction(x => x.BulkDelete(customers2));

// ...code...

context.ExecuteFutureAction();
```

[Online Example](https://dotnetfiddle.net/V6KsSl)

### Delete with Rows Affected

Use the [UseRowsAffected](/rows-affected) option to retrieve the number of rows affected by the `BulkDelete` operation.

This is useful when you need to verify how many rows were actually deleted for logging, validation, or reporting.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var resultInfo = new Z.BulkOperations.ResultInfo();

context.BulkDelete(customers, options =>
{
    options.UseRowsAffected = true;
    options.ResultInfo = resultInfo;
});

int rowsAffected = resultInfo.RowsAffected;
int rowsAffectedDeleted = resultInfo.RowsAffectedDeleted;
```

[Online Example](https://dotnetfiddle.net/ThTRXs)

### More Examples

Need a scenario not covered here?

There’s a good chance we already support it.

**[Contact us to discuss your scenario](/contact-us)**

## 🔑 Key Benefits

One of the main reasons people use our Bulk Delete is to **delete entities exactly the way they want** — without having to load them into memory or deal with tracking issues. You stay in control while getting top performance.

- ✅ **Delete the way you want:** Use custom keys, delete related entities (graph), or target specific conditions.
- ✅ **Extremely fast:** Delete thousands or millions of rows in seconds.
- ✅ **No need to load entities:** Avoid change tracking — delete directly from your data.
- ✅ **Flexible with hundreds of options:** Choose how relationships are handled, how keys are matched, and more.

## 🔍 What is supported?

Our library supports all the common scenarios — and almost everything you can do with EF Core and EF6!

- ✅ The latest Entity Framework Core version: EF Core 10
- ✅ All previous EF Core versions: EF Core 2 to 9
- ✅ All Entity Framework versions: EF6, EF5, EF4, and EF Classic  
- ✅ All major database providers: SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle  
- ✅ All inheritance mapping strategies: TPC, TPH, and TPT  
- ✅ Complex types / owned entity types  
- ✅ Enums  
- ✅ Value converters (EF Core)  
- ✅ And much more — even shadow properties!

### 🚀 Performance Comparison

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,200 ms       | 2,400 ms       | 6,000 ms       |
| BulkDelete      | 50 ms          | 55 ms          | 75 ms         |

[Try it in EF Core](https://dotnetfiddle.net/9r3vLC) | [Try it in EF6](https://dotnetfiddle.net/qYjiA9)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### 📊 Benchmark Results

The tables above give you a quick idea of the performance gains when using **Bulk Delete**.  
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

Here’s an example chart for **SQL Server – Bulk Delete** comparing **EF Core SaveChanges** vs **EF Extensions BulkDelete**:

![Benchmark EF Core vs EF Extensions – SQL Server - Bulk Delete](https://raw.githubusercontent.com/zzzprojects/EntityFramework-Extensions/master/images/benchmark-efcore-vs-efe-sqlserver-bulk-delete.png)

### Scenarios
The `BulkDelete` method is **fast** but also **flexible** to let you handle various scenarios in Entity Framework such as:

- [Delete with custom key](#delete-with-custom-key)
- [Delete with future action](#delete-with-future-action)
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness
- Reduce database load
- Reduce database round-trips

## Getting Started

### Bulk Delete
The `BulkDelete` and `BulkDeleteAsync` methods extend your `DbContext` to let you delete a large number of entities in your database.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(customers);

context.BulkDeleteAsync(customers, cancellationToken);
```

[Online Example)

### Bulk Delete with options
The `options` parameter lets you use a lambda expression to customize the way entities are deleted.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(customers, options => options.BatchSize = 100);
```

[Online Example](https://dotnetfiddle.net/lIUiH2)

### Why BulkDelete is faster than SaveChanges?
Deleting thousands of entities for a file importation is a typical scenario.

The `SaveChanges` method makes it quite impossible to handle this kind of situation due to the number of database round-trips required. The `SaveChanges` performs one database round-trip for every entity to delete. So, if you need to delete 10,000 entities, 10,000 database round-trips will be performed which is **INSANELY** slow.

The `BulkDelete` by contrast requires the minimum number of database round-trips possible. For example, under the hood for SQL Server, a `SqlBulkCopy` is performed first in a temporary table, then a `DELETE` from the temporary table to the destination table is performed which is the fastest way available.

## Bulk Delete Options

### Configuring Options

We already saw in previous article [Configuring Options](/configure-options) how to pass options to the `BulkDelete` method — but here’s a quick recap:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a lambda expression (only works with one option)
context.BulkDelete(list, options => options.IncludeGraph = true);

// Using a lambda expression with a body (works with one or multiple options)
context.BulkDelete(list, options =>
{
    options.IncludeGraph = true;
    options.ColumnPrimaryKeyExpression = x => new { x.ID };
});

// Using a `BulkOperationOption` instance
var options = context.CreateBulkOptions<EntitySimple>();
options.IncludeGraph = true;
options.ColumnPrimaryKeyExpression = x => new { x.ID };

context.BulkDelete(list, options);
```

> 💡 Tip: Using a `BulkOperationOption` instance is useful when you want to reuse the same configuration across multiple operations or keep your setup code more organized.

### Common Options

- Bulk Delete Behavior
   - **DeletePrimaryKeyAndFormula:** Specify a hardcoded SQL to include additional logic—along with the primary key—to check if the entity matches an existing row in the database. Only rows that also match the formula will be deleted.
   - **DeleteStagingTableFilterFormula:** Specify a hardcoded SQL if you want to filter which rows should be deleted using a staging table.
- Matched Behavior
   - **DeleteMatchedAndFormula:** After matching rows by primary key, you can specify an additional SQL condition to delete only the rows that also satisfy this formula.
   - **DeleteMatchedAndConditionExpression:**  After matching rows by primary key, you can specify additional properties using a lambda expression. All specified property values must match between the entity and the database for the row to be deleted.
   - **DeleteMatchedAndConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. All specified property values must match between the entity and the database for the row to be deleted.
   - **DeleteMatchedAndOneNotConditionExpression:** After matching rows by primary key, you can specify additional properties using a lambda expression. At least one of the specified property values must differ between the entity and the database for the row to be deleted.
   - **DeleteMatchedAndOneNotConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. At least one of the specified property values must differ between the entity and the database for the row to be deleted.
   - **IgnoreOnDeleteMatchedAndConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `DeleteMatchedAndConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnDeleteMatchedAndConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `DeleteMatchedAndConditionNames`, and all other properties will be used for the match.
   - **IgnoreOnDeleteMatchedAndOneNotConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `DeleteMatchedAndOneNotConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnDeleteMatchedAndOneNotConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `DeleteMatchedAndOneNotConditionNames`, and all other properties will be used for the match.
- Behavior
   - **IncludeGraph:** Set to `true` if you want to delete both the main entities and their related entities. For example, if you pass a list of `Order` that includes `OrderItem`, both will be deleted. Be careful: if you want to apply specific options to a related entity type, you’ll need to configure them using `IncludeGraphBuilder`. Only compatible with EF Core
   - **IncludeGraphBuilder:** Required only if `IncludeGraph = true` **and** you need to customize how a related entity type is deleted. Use a lambda expression to control how each entity in the graph should be deleted.
- Properties & Columns
   - **ColumnPrimaryKeyExpression:** Choose which properties should be part of the key by using a lambda expression. Only rows that match the key will be deleted.
   - **ColumnPrimaryKeyNames:** Choose which properties should be part of the key by using a list of strings. Only rows that match the key will be deleted.
- Optimization
   - **Batch:** Customize the `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` to improve performance and control how deleted entities are grouped and executed.
   - **Hint:** Use `QueryHint` or `TableHintSql` to apply SQL hints for additional performance tuning.
   - **UseTableLock:** Set to `true` to lock the destination table during the delete operation, which can improve performance by reducing row-level locks and avoiding lock escalation. This is especially useful when inserting a large number of rows.
- Providers Specific
   - **OracleDeleteTableHint:** Gets or sets a "DELETE" hint (for BulkDelete) for ORACLE only.
- General
   - **Audit:** Track deleted entities by using the `UseAudit` and `AuditEntries` options. [Learn more here](/audit)
   - **FutureAction:** Batch multiple delete operations and execute them later using the `ExecuteFuture` or `ExecuteFutureAsync` methods.
   - **Log:** Log all executed SQL statements using the `Log`, `UseLogDump`, and `LogDump` options. [Learn more here](/logging)
   - **RowsAffected:** Use `UseRowsAffected = true`, then access `ResultInfo.RowsAffected` or `ResultInfo.RowsAffectedDeleted` to get the number of entities deleted. [Learn more here](/rows-affected)

## Troubleshooting

### Lazy Loading + Include Graph (Before v10.5.7)

Before version **10.5.7**, `IncludeGraph` automatically traversed lazy navigations when lazy loading was enabled.

This behavior could unintentionally load additional related entities and cause the entire graph to be deleted, even if you only intended to delete part of it.

To avoid this behavior, you had to disable lazy loading before retrieving your entities:

```csharp
using (var context = new EntityContext())
{
    context.ChangeTracker.LazyLoadingEnabled = false;


    var invoices = context.Invoices.ToList();
    context.BulkDelete(invoices, options => options.IncludeGraph = true);
}
```

Starting with **v10.5.7**, this is no longer the default behavior. `IncludeGraph` only traverses already loaded navigations. If you want lazy navigations to be loaded automatically during graph traversal, set `LoadLazyNavigations` to `true`.

## Conclusion

The `BulkDelete` method is very powerful. One of its biggest benefits is that you don’t need to use the change tracker or retrieve your entities before deleting them (which often doesn’t make much sense anyway). The major benefit is the performance gain—but you can also delete using a custom key or even delete an entire entity graph.

Perfect when you want to delete thousands of rows fast—without slowing down your app.