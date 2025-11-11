---
Title: Bulk Synchronize in EF Core | Add or update or delete operations
MetaDescription: Efficiently synchronize Entity Framework data with EF Core Bulk Synchronize Extensions. Easily update, insert and delete large numbers of entities with customizable options for all EF versions, including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2025-11-11
---

# Bulk Synchronize /n Directly execute add, update, and delete operations to mirror data in EF Core

The `BulkSynchronize` method allows you to mirror data from your source to the database in EF Core. What exactly is a mirror operation?

- Rows that match the entity key are **UPDATED**.  
- Rows that exist in the source but not in the database are **INSERTED**.  
- Rows that exist in the database but not in the source are **DELETED**.

In other words, your destination table data becomes exactly like the list of entities you provide. The method is similar to [BulkMerge](/bulk-merge), but it also includes the **delete** step, making it a complete mirror.

It’s also possible to synchronize only a subset of your table with the `ColumnSynchronizeDeleteKeySubsetExpression` option, or to soft-delete rows with the `SynchronizeSoftDeleteFormula` option (which we will cover later).

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkSynchronize(customers);

// Easy to customize
context.BulkSynchronize(customers, options => {
    options.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```

[Online Example (EF Core)](https://dotnetfiddle.net/v4KQSX) | [Online Example (EF6)](https://dotnetfiddle.net/nZedku)

> ⚠️ **Warning**
> If you provide an **empty list**, the `BulkSynchronize` method will not be executed.
>
> * This is **intentional**: we want to avoid accidentally deleting all the data in your table.
> * If you are using the `ColumnSynchronizeDeleteKeySubsetExpression` option, there would be no way to know which subset of data should be deleted when the list is empty.

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

## Real Life Scenarios

### Synchronize and keep identity value
Your entity has an identity property, but you want to force to insert a specific value instead. The `SynchronizeKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers, options => options.SynchronizeKeepIdentity = true);
```

[Try it in EF Core](https://dotnetfiddle.net/PQ2DDi) | [Try it in EF6](https://dotnetfiddle.net/crxeJ3)

### Synchronize and include/exclude properties
You want to synchronize your entities but only for specific properties.

- `ColumnInputExpression`: This option lets you choose which properties to map.
- `IgnoreOnSynchronizeInsertExpression`: This option lets you ignore when inserting properties that are auto-mapped.
- `IgnoreOnSynchronizeUpdateExpression`: This option lets you ignore when updating properties that are auto-mapped.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customizeToSynchronize, options => {
    options.IgnoreOnSynchronizeInsertExpression = c => c.UpdatedDate;
    options.IgnoreOnSynchronizeUpdateExpression = c => c.CreatedDate;
});
```

[Try it in EF Core](https://dotnetfiddle.net/Dk60YN) | [Try it in EF6](https://dotnetfiddle.net/mOlppr)

### Synchronize a subset of the table
You want to synchronize your table, but only a subset of the table and not the whole table, such as only a specific user, type, or category that exists in the entities list provided.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers, options => {
    options.ColumnPrimaryKeyExpression = customer => customer.Name;
    options.ColumnSynchronizeDeleteKeySubsetExpression = customer => customer.Type;
});
```

[Try it in EF Core](https://dotnetfiddle.net/clr84M) | [Try it in EF6](https://dotnetfiddle.net/y5snLt)

Learn more here: https://entityframework-extensions.net/column-synchronize-delete-key-subset-expression

### Synchronize with custom key
You want to synchronize entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` lets you use any property or combination of properties as a key.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkSynchronize(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code);    
```

[Try it in EF Core](https://dotnetfiddle.net/oigfK6) | [Try it in EF6](https://dotnetfiddle.net/PYjmAJ)

### Synchronize with future action
You want to synchronize entities, but you want to defer the execution.

By default, `BulkSynchronize` is an immediate operation. That means, it's executed as soon as you call the method.

`FutureAction`: This option lets you defer the execution of a Bulk Synchronize.
`ExecuteFutureAction`: This option triggers and executes all pending `FutureAction`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.FutureAction(x => x.BulkSynchronize(customers));
context.FutureAction(x => x.BulkSynchronize(invoices));

// ...code...

context.ExecuteFutureAction();
```

[Try it in EF Core](https://dotnetfiddle.net/KmXE3m) | [Try it in EF6](https://dotnetfiddle.net/78FeXe) 

### More scenarios
Hundreds of scenarios have been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

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