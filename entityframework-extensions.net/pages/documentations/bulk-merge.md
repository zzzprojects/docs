---
Title: Bulk Merge in EF Core | Add or Update (Upsert) your entities
MetaDescription: Efficiently add or update Entity Framework data with EF Core Bulk Merge Extensions. Perform upsert operations on large numbers of entities with customizable options for all EF versions, including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2026-07-14
---

# EF Core Bulk Merge with Entity Framework Extensions

The `BulkMerge` method from Entity Framework Extensions is the most flexible way to perform **upsert** operations in EF Core and EF6. It allows you to insert new entities and update existing ones in a single operation.

When you perform a bulk merge, it behaves as follows:

* Rows that match the entity key are **UPDATED**.
* Rows that do not match any existing record are **INSERTED**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.BulkMergeAsync(customers);

// Easy to customize
await context.BulkMergeAsync(invoices, options => options.IncludeGraph = true);
```

[Online Example](https://dotnetfiddle.net/v08Jzy)

## Bulk Merge Example

### Merge with a Custom Key

The [ColumnPrimaryKeyExpression](/primary-key#using-columnprimarykeyexpression) and [ColumnPrimaryKeyNames](/primary-key#using-columnprimarykeynames) options let you define how existing rows are matched during the merge by using a custom key (or a combination of properties) instead of your entity's mapped primary key.

This is particularly useful when importing data from an external source where the matching key is based on a business identifier such as a code, SKU, or external ID.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using `ColumnPrimaryKeyExpression`
context.BulkMerge(customers, options => options.ColumnPrimaryKeyExpression = x => x.Code);

// Using `ColumnPrimaryKeyNames`
var customKeys = new List<string>() { nameof(Customer.Code) };
context.BulkMerge(customers, options => options.ColumnPrimaryKeyNames = customKeys);
```

[Online Example](https://dotnetfiddle.net/LqM4UJ)

### Merge with Identity Value

By default, when a new row is inserted during a merge, the database generates the value for identity columns. Use the `MergeKeepIdentity` option when you want to preserve the identity value from your entities instead.

This is useful when importing existing data where identity values must be preserved.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => options.MergeKeepIdentity = true);
```

[Online Example](https://dotnetfiddle.net/5MYQpK)

### Merge Only Specific Properties

By default, `BulkMerge` maps all properties for both the `INSERT` and `UPDATE` parts of the operation. You can use the following options to control exactly which properties are included.

All options below also have a `Names` equivalent if you prefer specifying property names instead of expressions.

* [ColumnInputExpression](/input-output-ignore#column-input): Specifies which properties should be included for both `INSERT` and `UPDATE`.
* [ColumnIgnoreExpression](/input-output-ignore#ignore): Excludes specific properties from both `INSERT` and `UPDATE`.
* [OnMergeInsertInputExpression](/input-output-ignore#onmerge-onsynchronize): Specifies which properties should be included only for the `INSERT` part.
* [OnMergeUpdateInputExpression](/input-output-ignore#onmerge-onsynchronize): Specifies which properties should be included only for the `UPDATE` part.
* [IgnoreOnMergeInsertExpression](/input-output-ignore#ignore-for-merge-example): Excludes specific properties only for the `INSERT` part.
* [IgnoreOnMergeUpdateExpression](/input-output-ignore#ignore-for-merge-example): Excludes specific properties only for the `UPDATE` part.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Ignore specific properties for INSERT and UPDATE
context.BulkMerge(customers, options =>
{
    options.IgnoreOnMergeInsertExpression = x => x.UpdatedDate;
    options.IgnoreOnMergeUpdateExpression = x => x.CreatedDate;
});
```

[Online Example](https://dotnetfiddle.net/VQkluJ)

### Merge with Related Entities (Include Graph)

Use this option when you want to merge entities and automatically merge all related entities (children, grandchildren, and more) linked through navigation properties.

* [IncludeGraph](/include-graph): Automatically merges all related entities linked through navigation properties.
* [IncludeGraphOperationBuilder](/include-graph#includegraphoperationbuilder): Lets you customize how a specific entity type is merged.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(invoices, options => options.IncludeGraph = true);
```

[Online Example](https://dotnetfiddle.net/imc0r5)

### Merge with Rows Affected

Use the [UseRowsAffected](/rows-affected) option to retrieve the number of rows affected by the `BulkMerge` operation.

This is useful when you need to verify how many rows were inserted or updated for logging, validation, or reporting.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var resultInfo = new Z.BulkOperations.ResultInfo();

context.BulkMerge(customers, options =>
{
    options.UseRowsAffected = true;
    options.ResultInfo = resultInfo;
});

int rowsAffected = resultInfo.RowsAffected;
int rowsAffectedInserted = resultInfo.RowsAffectedInserted;
int rowsAffectedUpdated = resultInfo.RowsAffectedUpdated;
```

[Online Example](https://dotnetfiddle.net/0pphXF)

### More Examples

Need a scenario not covered here?

There’s a good chance we already support it.

**[Contact us to discuss your scenario](/contact-us)**

## 🔑 Key Benefits

One of the main reasons people use our Bulk Merge is to **perform add or update (upsert) operations exactly the way they want**. You get full control over how rows are matched, which values are inserted, and how updates are applied — all with exceptional performance.

- ✅ **Add or update the way you want:** Define custom keys, control which properties to insert or update, and apply conditions.
- ✅ **Extremely fast:** Handle thousands or millions of upserts in seconds.
- ✅ **No need to load entities:** Save resources by working directly with your data, no tracking required.
- ✅ **Flexible with hundreds of options:** Customize behavior to fit your rules — from conditional updates to advanced key matching.

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
| SaveChanges     | 4,000 ms       | Too long...     | Way way too long... |
| BulkMerge       | 80 ms          | 110 ms         | 170 ms         |

[Try it in EF Core](https://dotnetfiddle.net/hmDtiI) | [Try it in EF6](https://dotnetfiddle.net/Erk8R3)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### 📊 Benchmark Results

The tables above give you a quick idea of the performance gains when using **Bulk Merge**.  
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

Here’s an example chart for **SQL Server – Bulk Merge** comparing **EF Core SaveChanges** vs **EF Extensions BulkMerge**:

![Benchmark EF Core vs EF Extensions – SQL Server - Bulk Merge](https://raw.githubusercontent.com/zzzprojects/EntityFramework-Extensions/master/images/benchmark-efcore-vs-efe-sqlserver-bulk-merge.png)

### Scenarios
The `BulkMerge` method is **fast** but also **flexible** to let you handle various scenarios in Entity Framework such as:

- [Merge and keep identity value](#merge-and-keep-identity-value)
- [Merge with custom key](#merge-with-custom-key)
- [Merge and include/exclude properties](#merge-and-includeexclude-properties)
- [Merge with related child entities (Include Graph)](#merge-with-related-child-entities-include-graph)
- [Merge with future action](#merge-with-future-action)
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness
- Reduce database load
- Reduce database round-trips

## Getting Started

### Bulk Merge
The `BulkMerge` and `BulkMergeAsync` methods extend your `DbContext` to let you merge a large number of entities in your database.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers);

context.BulkMergeAsync(customers, cancellationToken);
```

[Try it in EF Core](https://dotnetfiddle.net/GqKIoc) | [Try it in EF6](https://dotnetfiddle.net/0Ba6ZB)

### Bulk Merge with options
The `options` parameter lets you use a lambda expression to customize the way entities are inserted or updated.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code);
```

[Try it in EF Core](https://dotnetfiddle.net/Lzbh2H) | [Try it in EF6](https://dotnetfiddle.net/JsHWWm)

### Why BulkMerge is faster than SaveChanges?
Merging thousands of entities for a file importation is a typical scenario.

The `AddOrUpdate` method performs a database round-trip for every entity to check if it already exists. The `DetectChanges` method is also called for every entity which makes this method even slower (it's like using the `Add` method instead of `AddRange`).

The `SaveChanges` method performs one database round-trip for every entity to update.

So, if you need to merge 10,000 entities, 20,000 database round-trips will be performed + 10,000 `DetectChanges` calls which is **INSANELY** slow.

The `BulkMerge` in contrast requires the minimum number of database round-trips possible. For example, under the hood for SQL Server, a `SqlBulkCopy` is performed first in a temporary table, then a `MERGE` from the temporary table to the destination table is performed which is the fastest way available.

## Bulk Merge Options

### Configuring Options

We already saw in previous article [Configuring Options](/configure-options) how to pass options to the `BulkMerge` method — but here’s a quick recap:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a lambda expression (only works with one option)
context.BulkMerge(list, options => options.MergeKeepIdentity = true);

// Using a lambda expression with a body (works with one or multiple options)
context.BulkMerge(list, options =>
{
    options.MergeKeepIdentity = true;
    options.ColumnPrimaryKeyExpression = x => new { x.ID };
});

// Using a `BulkOperationOption` instance
var options = context.CreateBulkOptions<EntitySimple>();
options.MergeKeepIdentity = true;
options.ColumnPrimaryKeyExpression = x => new { x.ID };

context.BulkMerge(list, options);
```

> 💡 Tip: Using a `BulkOperationOption` instance is useful when you want to reuse the same configuration across multiple operations or keep your setup code more organized.

### Common Options

- Bulk Merge Behavior
   - **IgnoreOnMergeInsert:** Set to `false` if you want to ignore the insert phase part of the merge operation.
   - **IgnoreOnMergeUpdate:** Set to `false` if you want to ignore the update phase part of the merge operation.
   - **MergeKeepIdentity:** Set to `true` if you want to insert entities with their identity value. For SQL Server, the library will automatically handle the `SET IDENTITY_INSERT [tableName] ON` and `SET IDENTITY_INSERT [tableName] OFF` commands.
   - **MergeNotMatchedAndFormula:** Specify a hardcoded SQL if you want to add custom logic to filter which rows should be inserted during the insert phase part of the merge operation.
   - **MergePrimaryKeyAndFormula:** Specify a hardcoded SQL to include additional logic—along with the primary key—to check if the entity matches an existing row in the database. Only rows that also match the formula will be updated, all other rows will be inserted.
   - **MergeStagingTableFilterFormula:** Specify a hardcoded SQL if you want to filter which rows should be merged (added or updated) using a staging table.
- Coalesce Behavior
   - **OnMergeUpdateUseCoalesce:** For each property, during the update phase of a merge operation, if the source value is `null`, the destination value will stay unchanged. This behaves like `ISNULL(StagingTable.ColumnName, DestinationTable.ColumnName)` in SQL Server.
   - **OnMergeUpdateUseCoalesceDestination:** For each property, during the update phase of a merge operation, the destination value will only be updated if its current value in the database is `null`. This behaves like `ISNULL(DestinationTable.ColumnName, StagingTable.ColumnName)` in SQL Server.
   - **CoalesceOnMergeUpdateExpression:** Use a lambda expression to specify which properties should apply the `OnUpdateUseCoalesce` logic during the update phase of a merge operation.
   - **CoalesceOnMergeUpdateNames:** Use a list of strings to specify which properties should apply the `OnUpdateUseCoalesce` logic during the update phase of a merge operation.
   - **CoalesceDestinationOnMergeUpdateExpression:** Use a lambda expression to specify which properties should apply the `OnUpdateUseCoalesceDestination` logic during the update phase of a merge operation.
   - **CoalesceDestinationOnMergeUpdateNames:** Use a list of strings to specify which properties should apply the `OnUpdateUseCoalesceDestination` logic during the update phase of a merge operation.
- Matched Behavior
   - **MergeMatchedAndFormula:** After matching rows by primary key, you can specify an additional SQL condition to update only the rows that also satisfy this formula. Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **MergeMatchedAndConditionExpression:**  After matching rows by primary key, you can specify additional properties using a lambda expression. All specified property values must match between the entity and the database for the row to be updated. Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **MergeMatchedAndConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. All specified property values must match between the entity and the database for the row to be updated. Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **MergeMatchedAndOneNotConditionExpression:** After matching rows by primary key, you can specify additional properties using a lambda expression. At least one of the specified property values must differ between the entity and the database for the row to be updated. Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **MergeMatchedAndOneNotConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. At least one of the specified property values must differ between the entity and the database for the row to be updated.  Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **IgnoreOnMergeMatchedAndConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `MergeMatchedAndConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnMergeMatchedAndConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `MergeMatchedAndConditionNames`, and all other properties will be used for the match.
   - **IgnoreOnMergeMatchedAndOneNotConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `MergeMatchedAndOneNotConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnMergeMatchedAndOneNotConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `MergeMatchedAndOneNotConditionNames`, and all other properties will be used for the match.
- Behavior
   - **AutoTruncate:** Set to `true` if you want string values to be automatically truncated to match the maximum database length before being merged (added or updated). This option is especially useful because `SqlCommand` and `SqlBulkCopy` can behave differently when a string is too long. (See [Issue #333](https://github.com/zzzprojects/EntityFramework-Extensions/issues/333#issuecomment-1041494634))
   - **ExplicitValueResolutionMode:** Specify how explicit values for columns (that aren’t usually expected to be set) should be handled. In EF Core, these values are always inserted. In EF Extensions, you need to tell how you want to handle them. [Learn more here](https://entityframework-extensions.net/explicit-value-resolution-mode)
   - **IncludeGraph:** Set to `true` if you want to merge (add or update) both the main entities and their related entities. For example, if you pass a list of `Order` that includes `OrderItem`, both will be merged. Be careful: if you want to apply specific options to a related entity type, you’ll need to configure them using `IncludeGraphBuilder`.
   - **IncludeGraphBuilder:** Required only if `IncludeGraph = true` **and** you need to customize how a related entity type is merged. Use a lambda expression to control how each entity in the graph should be merged (added or updated) — for example, to define how child entities are linked to their parent or how IDs should be propagated.
- Properties & Columns
   - **ColumnInputExpression:** Choose which properties should be merged (added or updated) by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputNames:** Choose which properties should be merged (added or updated) by using a list of strings to select them. All other properties will be ignored.
   - **ColumnInputOutputExpression:** Choose which properties should be merged (added or updated)  **and** outputted by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputOutputNames:** Choose which properties should be merged (added or updated)  **and** outputted by using a list of strings to select them. All other properties will be ignored.
   - **ColumnOutputExpression:** Choose which properties should be outputted after the merge by using a lambda expression to select them.
   - **ColumnOutputNames:** Choose which properties should be outputted after the merge by using a lambda expression to select them.
   - **ColumnPrimaryKeyExpression:** Choose which properties should be part of the key by using a lambda expression. Only rows that match the key will be updated, all other rows will be inserted.
   - **ColumnPrimaryKeyNames:** Choose which properties should be part of the key by using a list of strings. Only rows that match the key will be updated, all other rows will be inserted.
   - **OnMergeInsertInputExpression:** Choose which properties using a lambda expression should be inserted during the insert phase of the merge operation. This option doesn't affect properties that will be updated.
   - **OnMergeInsertInputNames:** Choose which properties using a list of strings should be inserted during the insert phase of the merge operation. This option doesn't affect properties that will be updated.
   - **OnMergeUpdateInputExpression:** Choose which properties using a lambda expression should be updated during the updated phase of the merge operation. This option doesn't affect properties that will be inserted.
   - **OnMergeUpdateInputNames:** Choose which properties using a list of strings should be updated during the updated phase of the merge operation. This option doesn't affect properties that will be inserted.
   - **IgnoreOnMergeInsertExpression:** Choose which properties should be ignored by using a lambda expression to select them. All other properties will be inserted. This option does **not** affect the `UPDATE` part of the merge.
   - **IgnoreOnMergeInsertNames:** Choose which properties should be ignored by using a list of strings to select them. All other properties will be inserted. This option does **not** affect the `UPDATE` part of the merge.
   - **IgnoreOnMergeUpdateExpression:** Choose which properties should be ignored by using a lambda expression to select them. All other properties will be inserted. This option does **not** affect the `INSERT` part of the merge.
   - **IgnoreOnMergeUpdateNames:** Choose which properties should be ignored by using a list of strings to select them. All other properties will be inserted. This option does **not** affect the `INSERT` part of the merge.
- Optimization
   - **Batch:** Customize the `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` to improve performance and control how merged (add or update) entities are grouped and executed.
   - **Hint:** Use `QueryHint` or `TableHintSql` to apply SQL hints for additional performance tuning.
   - **UseTableLock:** Set to `true` to lock the destination table during the merge operation, which can improve performance by reducing row-level locks and avoiding lock escalation. This is especially useful when inserting a large number of rows.
- Providers Specific
   - **OracleMergeInsertTableHint:** Gets or sets a "INSERT" hint (for BulkMerge) for ORACLE only.
   - **OracleMergeUpdateTableHint:** Gets or sets a "UPDATE" hint (for BulkMerge) for ORACLE only.
   - **UsePostgreSqlInsertOnConflictDoNothing:** Set to `true` if you want to silently ignore any conflict that would otherwise trigger a constraint violation error, such as a duplicate key.
   - **UsePostgreSqlInsertOverridingSystemValue:** Set to `true` if you want the values provided in the `INSERT` statement (during the `BulkMerge` operation) to take precedence over any default values defined at the database level for system columns. This is useful when you need to insert explicit values for columns like timestamps that are normally managed by the database.
   - **UsePostgreSqlInsertOverridingUserValue:** Set to `true` if you want the values in the `INSERT` statement (during the `BulkMerge` operation) to override any user-defined default values set at the database level. This is helpful when you want the application's data to take priority — especially during automated or bulk inserts.
   - **UsePostgreOnMergeSqlInsertOnConflictDoUpdate:** Set to `true` if you want to instead use a stragegy using the SQL `ON CONFLICT (key) DO UPDATE` clause. 
- General
   - **Audit:** Track merged (add or update) entities by using the `UseAudit` and `AuditEntries` options. [Learn more here](/audit)
   - **FutureAction:** Batch multiple merge operations and execute them later using the `ExecuteFuture` or `ExecuteFutureAsync` methods.
   - **Log:** Log all executed SQL statements using the `Log`, `UseLogDump`, and `LogDump` options. [Learn more here](/logging)
   - **RowsAffected:** Use `UseRowsAffected = true`, then access `ResultInfo.RowsAffected` or `ResultInfo.RowsAffectedInserted` and `ResultInfo.RowsAffectedUpdated` to get the number of entities merged (added or updated). [Learn more here](/rows-affected)

## Troubleshooting

### InsertIfNotExists doesn't work

This behavior is expected. The `InsertIfNotExists` option only applies to [BulkInsert](/bulk-insert) and [BulkInsertOptimized](/bulk-insert-optimized).

The `BulkMerge` method already inserts new rows automatically when they don’t exist and updates existing ones — that’s its main purpose.
So, `InsertIfNotExists` doesn’t apply when using `BulkMerge`.

If you want to skip the insert part during a merge, you can use the following option instead:

```csharp
options => options.IgnoreOnMergeInsert = true;
```

### Lazy Loading + Include Graph (Before v10.5.7)

Before version **10.5.7**, `IncludeGraph` automatically traversed lazy navigations when lazy loading was enabled.

This behavior could unintentionally load additional related entities and cause the entire graph to be inserted, updated, or merged, even if you only intended to process part of it.

To avoid this behavior, you had to disable lazy loading before retrieving your entities:

```csharp
using (var context = new EntityContext())
{
    context.ChangeTracker.LazyLoadingEnabled = false;

    var invoices = context.Invoices.ToList();

    // ...update invoice properties...

    context.BulkMerge(invoices, options => options.IncludeGraph = true);
}
````

Starting with **v10.5.7**, this is no longer the default behavior. `IncludeGraph` only traverses already loaded navigations. If you want lazy navigations to be loaded automatically during graph traversal, set `LoadLazyNavigations` to `true`.

## Conclusion

The `BulkMerge` method adds an "Add or Update" feature that you won’t find with the regular `SaveChanges` method. On top of that, it gives you full control over how you want to insert or update your entities using a variety of available options.