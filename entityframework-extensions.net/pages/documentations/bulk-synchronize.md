---
Title: Bulk Synchronize in EF Core | Add or Update or Delete your entities
MetaDescription: Efficiently synchronize Entity Framework data with EF Core Bulk Synchronize Extensions. Easily update, insert and delete large numbers of entities with customizable options for all EF versions, including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2026-05-25
---

# EF Core Bulk Synchronize with Entity Framework Extensions

The `BulkSynchronize` method from Entity Framework Extensions is the most flexible way to perform **mirror** operations in EF Core and EF6. It allows you to insert new entities, update existing ones, and delete rows that are no longer part of your source in a single operation.

When you perform a bulk synchronize, it behaves as follows:

* Rows that match the entity key are **UPDATED**.
* Rows that exist in your source but not in the database are **INSERTED**.
* Rows that exist in the database but not in your source are **DELETED**.

In other words, your destination table becomes exactly like the list of entities you provide. The method is similar to [BulkMerge](/bulk-merge), but it also includes the **DELETE** step.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkSynchronize(customers);

// Easy to customize
context.BulkSynchronize(customers, options =>
{
    options.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```

[Online Example](https://dotnetfiddle.net/v4KQSX)

> ⚠️ **Warning**
> If you provide an **empty list**, the `BulkSynchronize` method will not be executed.
>
> * This is **intentional**: we want to avoid accidentally deleting all the data in your table.
> * If you are using the `ColumnSynchronizeDeleteKeySubsetExpression` option, there would be no way to know which subset of data should be deleted when the list is empty.

## Bulk Merge Example

### Synchronize with a Custom Key

The [ColumnPrimaryKeyExpression](/primary-key#using-columnprimarykeyexpression) and [ColumnPrimaryKeyNames](/primary-key#using-columnprimarykeynames) options let you define how rows are matched during the synchronization by using a custom key (or a combination of properties) instead of your entity's mapped primary key.

This is particularly useful when importing data from an external source where matching must be based on a business identifier such as a code, SKU, or external ID.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using `ColumnPrimaryKeyExpression`
context.BulkSynchronize(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code);

// Using `ColumnPrimaryKeyNames`
var customKeys = new List<string>() { nameof(Customer.Code) };
context.BulkSynchronize(customers, options => options.ColumnPrimaryKeyNames = customKeys);
```

[Online Example](https://dotnetfiddle.net/oigfK6)

### Synchronize a Subset of the Table

By default, `BulkSynchronize` compares your entities against the entire destination table. Use the [ColumnSynchronizeDeleteKeySubsetExpression](/column-synchronize-delete-key-subset-expression) option when you want to limit the synchronization to only a specific subset of rows.

This is useful when synchronizing data for a specific user, type, category, or any other logical partition of your table.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers, options =>
{
    options.ColumnPrimaryKeyExpression = customer => customer.Name;
    options.ColumnSynchronizeDeleteKeySubsetExpression = customer => customer.Type;
});
```

[Online Example](https://dotnetfiddle.net/clr84M)

### Synchronize with Identity Value

By default, when a new row is inserted during the synchronization, the database generates the value for identity columns. Use the `SynchronizeKeepIdentity` option when you want to preserve the identity value from your entities instead.

This is useful when importing existing data where identity values must be preserved.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers, options => options.SynchronizeKeepIdentity = true);
```

[Online Example](https://dotnetfiddle.net/PQ2DDi)

### Synchronize Only Specific Properties

By default, `BulkSynchronize` maps all properties for both the `INSERT` and `UPDATE` parts of the operation. You can use the following options to control exactly which properties are included.

All options below also have a `Names` equivalent if you prefer specifying property names instead of expressions.

* [ColumnInputExpression](/input-output-ignore#column-input): Specifies which properties should be included for both `INSERT` and `UPDATE`.
* [ColumnIgnoreExpression](/input-output-ignore#ignore): Excludes specific properties from both `INSERT` and `UPDATE`.
* [OnSynchronizeInsertInputExpression](/input-output-ignore#onmerge-onsynchronize): Specifies which properties should be included only for the `INSERT` part.
* [OnSynchronizeUpdateInputExpression](/input-output-ignore#onmerge-onsynchronize): Specifies which properties should be included only for the `UPDATE` part.
* [IgnoreOnSynchronizeInsertExpression](/input-output-ignore#ignore-for-merge-example): Excludes specific properties only for the `INSERT` part.
* [IgnoreOnSynchronizeUpdateExpression](/input-output-ignore#ignore-for-merge-example): Excludes specific properties only for the `UPDATE` part.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Ignore specific properties for INSERT and UPDATE
context.BulkSynchronize(customers, options =>
{
    options.IgnoreOnSynchronizeInsertExpression = c => c.UpdatedDate;
    options.IgnoreOnSynchronizeUpdateExpression = c => c.CreatedDate;
});
```

[Online Example](https://dotnetfiddle.net/Dk60YN)

### Synchronize with Rows Affected

Use the [UseRowsAffected](/rows-affected) option to retrieve the number of rows affected by the `BulkSynchronize` operation.

This is useful when you need to verify how many rows were inserted, updated, or deleted for logging, validation, or reporting.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var resultInfo = new Z.BulkOperations.ResultInfo();

context.BulkSynchronize(customers, options =>
{
    options.UseRowsAffected = true;
    options.ResultInfo = resultInfo;
});

int rowsAffected = resultInfo.RowsAffected;
int rowsAffectedInserted = resultInfo.RowsAffectedInserted;
int rowsAffectedUpdated = resultInfo.RowsAffectedUpdated;
int rowsAffectedDeleted = resultInfo.RowsAffectedDeleted;
```

[Online Example](https://dotnetfiddle.net/REPLACE_ME)

## 🔑 Key Benefits

One of the main reasons people use our Bulk Synchronize is to **perform add, update, and delete operations exactly the way they want**. Whether you want to remove rows not in your list, soft delete them, or control which properties to update, this method gives you complete control.

- ✅ **Add, update, and delete the way you want:** Customize how each operation is handled — from hard deletes to soft deletes and conditional updates.
- ✅ **All-in-one operation:** Handle full data reconciliation in a single method call.
- ✅ **No need to load entities:** Update your database directly with high performance and low memory usage.
- ✅ **Flexible with hundreds of options:** Fine-tune key matching, update logic, delete strategy, and more.

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

### Scenarios
The `BulkSynchronize` method is **fast** but also **flexible** to let you handle various scenarios in Entity Framework such as:
- [Synchronize and keep identity value](#synchronize-and-keep-identity-value)
- [Synchronize and include/exclude properties](#synchronize-and-includeexclude-properties)
- [Synchronize a subset of the table](#synchronize-a-subset-of-the-table)
- [Synchronize with custom key](#synchronize-with-custom-key)
- [Synchronize with future action](#synchronize-with-future-action)
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness
- Reduce database load
- Reduce database round-trips

## Getting Started

### Bulk Synchronize
The `BulkSynchronize` and `BulkSynchronizeAsync` methods extend your `DbContext` to let you synchronize a large number of entities in your database.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers);

context.BulkSynchronizeAsync(customers, cancellationToken);
```

[Try it in EF Core](https://dotnetfiddle.net/la3HQL) | [Try it in EF6](https://dotnetfiddle.net/yPs4WF)

### Bulk Synchronize with options
The `options` parameter lets you use a lambda expression to customize the way entities are synchronized.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers, options => options.BatchSize = 100);

context.BulkSynchronize(customers, options => {
    options.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```

[Try it in EF Core](https://dotnetfiddle.net/edudfD) | [Try it in EF6](https://dotnetfiddle.net/FX3Quf)

## Bulk Synchronize Options

### Configuring Options

We already saw in previous article [Configuring Options](/configure-options) how to pass options to the `BulkSynchronize` method — but here’s a quick recap:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a lambda expression (only works with one option)
context.BulkSynchronize(list, options => options.SynchronizeKeepIdentity = true);

// Using a lambda expression with a body (works with one or multiple options)
context.BulkSynchronize(list, options =>
{
    options.SynchronizeKeepIdentity = true;
    options.ColumnPrimaryKeyExpression = x => new { x.ID };
});

// Using a `BulkOperationOption` instance
var options = context.CreateBulkOptions<EntitySimple>();
options.SynchronizeKeepIdentity = true;
options.ColumnPrimaryKeyExpression = x => new { x.ID };

context.BulkSynchronize(list, options);
```

> 💡 Tip: Using a `BulkOperationOption` instance is useful when you want to reuse the same configuration across multiple operations or keep your setup code more organized.

### Common Options

- Bulk Synchronize Behavior
   - **ColumnSynchronizeDeleteKeySubsetExpression:** Use a lambda expression to define an additional key, so that only a subset of the table is synchronized — instead of synchronizing the entire table.
   - **ColumnSynchronizeDeleteKeySubsetNames:** Use a list of strings to define an additional key, so that only a subset of the table is synchronized — instead of synchronizing the entire table.
   - **ColumnSynchronizeDeleteKeySubsetFormula:** Use a hardcoded SQL to define an additional key, so that only a subset of the table is synchronized — instead of synchronizing the entire table.
   - **SynchronizeSoftDeleteFormula:** Use a hardcoded SQL statement to define how the row should be updated for a soft delete instead of being hard deleted.
   - **SynchronizeIgnoreInsert:** Set to `false` if you want to ignore the insert phase part of the synchronize operation.
   - **SynchronizeIgnoreUpdate:** Set to `false` if you want to ignore the update phase part of the synchronize operation.
   - **SynchronizeOnlyDelete:** Set to `true` to perform only the delete phase part of the synchronize operation. So the insert and update phase will be ignored.
   - **SynchronizeKeepIdentity:** Set to `true` if you want to insert entities with their identity value. For SQL Server, the library will automatically handle the `SET IDENTITY_INSERT [tableName] ON` and `SET IDENTITY_INSERT [tableName] OFF` commands.
   - **SynchronizeNotMatchedAndFormula:** Specify a hardcoded SQL if you want to add custom logic to filter which rows should be inserted during the insert phase part of the synchronize operation.
   - **SynchronizePrimaryKeyAndFormulaMerge:** Specify a hardcoded SQL to include additional logic—along with the primary key—to check if the entity matches an existing row in the database. Only rows that also match the formula will be updated, all others rows will be inserted.
   - **SynchronizePrimaryKeyAndFormulaDelete:** Specify a hardcoded SQL to include additional logic—along with the primary key—to check if the entity matches an existing row in the database. Only rows that also match the formula will be deleted.
   - **SynchronizeDeleteDestinationTableFilterFormula:** Specify a hardcoded SQL statement to filter which rows can be deleted from the destination table. This helps ensure that no unwanted rows are deleted.
- Coalesce Behavior
   - **OnSynchronizeUpdateUseCoalesce:** For each property, during the update phase of a synchronize operation, if the source value is `null`, the destination value will stay unchanged. This behaves like `ISNULL(StagingTable.ColumnName, DestinationTable.ColumnName)` in SQL Server.
   - **OnSynchronizeUpdateUseCoalesceDestination:** For each property, during the update phase of a synchronize operation, the destination value will only be updated if its current value in the database is `null`. This behaves like `ISNULL(DestinationTable.ColumnName, StagingTable.ColumnName)` in SQL Server.
   - **CoalesceOnSynchronizeUpdateExpression:** Use a lambda expression to specify which properties should apply the `OnUpdateUseCoalesce` logic during the update phase of a synchronize operation.
   - **CoalesceOnSynchronizeUpdateNames:** Use a list of strings to specify which properties should apply the `OnUpdateUseCoalesce` logic during the update phase of a synchronize operation.
   - **CoalesceDestinationOnSynchronizeUpdateExpression:** Use a lambda expression to specify which properties should apply the `OnUpdateUseCoalesceDestination` logic during the update phase of a synchronize operation.
   - **CoalesceDestinationOnSynchronizeUpdateNames:** Use a list of strings to specify which properties should apply the `OnUpdateUseCoalesceDestination` logic during the update phase of a synchronize operation.
- Matched Behavior
   - **SynchronizeMatchedAndFormula:** After matching rows by primary key, you can specify an additional SQL condition to update (from the synchronize) only the rows that also satisfy this formula. Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **SynchronizeMatchedAndConditionExpression:**  After matching rows by primary key, you can specify additional properties using a lambda expression. All specified property values must match between the entity and the database for the row to be updated (from the synchronize). Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **SynchronizeMatchedAndConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. All specified property values must match between the entity and the database for the row to be updated (from the synchronize). Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **SynchronizeMatchedAndOneNotConditionExpression:** After matching rows by primary key, you can specify additional properties using a lambda expression. At least one of the specified property values must differ between the entity and the database for the row to be updated (from the synchronize). Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **SynchronizeMatchedAndOneNotConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. At least one of the specified property values must differ between the entity and the database for the row to be updated (from the synchronize). Rows that do not satisfy the matched condition will be skipped entirely—they won’t be updated or inserted.
   - **IgnoreOnSynchronizeMatchedAndConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `SynchronizeMatchedAndConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnSynchronizeMatchedAndConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `SynchronizeMatchedAndConditionNames`, and all other properties will be used for the match.
   - **IgnoreOnSynchronizeMatchedAndOneNotConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `SynchronizeMatchedAndOneNotConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnSynchronizeMatchedAndOneNotConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `SynchronizeMatchedAndOneNotConditionNames`, and all other properties will be used for the match.
- Behavior
   - **AutoTruncate:** Set to `true` if you want string values to be automatically truncated to match the maximum database length before being synchronized. This option is especially useful because `SqlCommand` and `SqlBulkCopy` can behave differently when a string is too long. (See [Issue #333](https://github.com/zzzprojects/EntityFramework-Extensions/issues/333#issuecomment-1041494634))
- Properties & Columns
   - **ColumnInputExpression:** Choose which properties should be synchronized (added or updated) by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputNames:** Choose which properties should be synchronized (added or updated) by using a list of strings to select them. All other properties will be ignored.
   - **ColumnInputOutputExpression:** Choose which properties should be synchronized (added or updated)  **and** outputted by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputOutputNames:** Choose which properties should be synchronized (added or updated)  **and** outputted by using a list of strings to select them. All other properties will be ignored.
   - **ColumnOutputExpression:** Choose which properties should be outputted after the synchronize by using a lambda expression to select them.
   - **ColumnOutputNames:** Choose which properties should be outputted after the synchronize by using a lambda expression to select them.
   - **ColumnPrimaryKeyExpression:** Choose which properties should be part of the key by using a lambda expression. Only rows that match the key will be updated, all others rows will be inserted.
   - **ColumnPrimaryKeyNames:** Choose which properties should be part of the key by using a list of strings. Only rows that match the key will be updated, all others rows will be inserted.
   - **OnSynchronizeInsertInputExpression:** Choose which properties using a lambda expression should be inserted during the insert phase of the synchronize operation. This option doesn't affect properties that will be updated.
   - **OnSynchronizeInsertInputNames:** Choose which properties using a list of strings should be inserted during the insert phase of the synchronize operation. This option doesn't affect properties that will be updated.
   - **OnSynchronizeUpdateInputExpression:** Choose which properties using a lambda expression should be updated during the updated phase of the synchronize operation. This option doesn't affect properties that will be inserted.
   - **OnSynchronizeUpdateInputNames:** Choose which properties using a list of strings should be updated during the updated phase of the synchronize operation. This option doesn't affect properties that will be inserted.
   - **IgnoreOnSynchronizeInsertExpression:** Choose which properties should be ignored by using a lambda expression to select them. All other properties will be inserted. This option does **not** affect the `UPDATE` part of the synchronize.
   - **IgnoreOnSynchronizeInsertNames:** Choose which properties should be ignored by using a list of strings to select them. All other properties will be inserted. This option does **not** affect the `UPDATE` part of the synchronize.
   - **IgnoreOnSynchronizeUpdateExpression:** Choose which properties should be ignored by using a lambda expression to select them. All other properties will be inserted. This option does **not** affect the `INSERT` part of the synchronize.
   - **IgnoreOnSynchronizeUpdateNames:** Choose which properties should be ignored by using a list of strings to select them. All other properties will be inserted. This option does **not** affect the `INSERT` part of the synchronize.
- Optimization
   - **Batch:** Customize the `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` to improve performance and control how synchronized entities are grouped and executed.
   - **Hint:** Use `QueryHint` or `TableHintSql` to apply SQL hints for additional performance tuning.
   - **UseTableLock:** Set to `true` to lock the destination table during the synchronize operation, which can improve performance by reducing row-level locks and avoiding lock escalation. This is especially useful when inserting a large number of rows.
- Providers Specific
   - **UsePostgreSqlInsertOnConflictDoNothing:** Set to `true` if you want to silently ignore any conflict that would otherwise trigger a constraint violation error, such as a duplicate key.
   - **UsePostgreSqlInsertOverridingSystemValue:** Set to `true` if you want the values provided in the `INSERT` statement (during the `BulkSynchronize` operation) to take precedence over any default values defined at the database level for system columns. This is useful when you need to insert explicit values for columns like timestamps that are normally managed by the database.
   - **UsePostgreSqlInsertOverridingUserValue:** Set to `true` if you want the values in the `INSERT` statement (during the `BulkSynchronize` operation) to override any user-defined default values set at the database level. This is helpful when you want the application's data to take priority — especially during automated or bulk inserts.
   - **UsePostgreOnMergeSqlInsertOnConflictDoUpdate:** Set to `true` if you want to instead use a stragegy using the SQL `ON CONFLICT (key) DO UPDATE` clause.
- General
   - **Audit:** Track synchronized entities by using the `UseAudit` and `AuditEntries` options. [Learn more here](/audit)
   - **FutureAction:** Batch multiple synchronize operations and execute them later using the `ExecuteFuture` or `ExecuteFutureAsync` methods.
   - **Log:** Log all executed SQL statements using the `Log`, `UseLogDump`, and `LogDump` options. [Learn more here](/logging)
   - **RowsAffected:** Use `UseRowsAffected = true`, then access `ResultInfo.RowsAffected`, `ResultInfo.RowsAffectedInserted`, `ResultInfo.RowsAffectedUpdated`, `ResultInfo.RowsAffectedDeleted`, `RowsAffectedSoftDeleted` to get the number of entities synchronized (added, updated, deleted). [Learn more here](/rows-affected)


## Conclusion

The `BulkSynchronize` method adds an "Add, Update, or Delete" feature. As we’ve seen, it allows you to synchronize an entire table or just a subset of it. Like [BulkMerge](/bulk-merge), it offers many options to control how your entities are inserted or updated. In addition, it also lets you control how entities are deleted—and even how to soft delete them if needed.