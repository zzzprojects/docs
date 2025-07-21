---
Title: Bulk Update in EF Core | Optimize the way you update your entities
MetaDescription: The BulkUpdate method is the most flexible way to update your entities in EF Core. It allows you to customize how your entities will be updated, such as by specifying a custom key, updating only a few properties, and much more. - try it now.
LastMod: 2025-06-23
---

# Bulk Update /n Easily customize and optimize your entity updates in EF Core now

The `BulkUpdate` method is the most flexible way to update your entities in EF Core. It allows you to customize how your entities will be updated, such as by specifying a custom key, updating only a few properties, and much more.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkUpdate(customers);

// Easy to customize
context.BulkUpdate(customers, options => options.IncludeGraph = true);
```

[Online Example (EF Core)](https://dotnetfiddle.net/Cwn8NC) | [Online Example (EF6)](https://dotnetfiddle.net/5wBlVh)

## 🔑 Key Benefits

One of the main reasons people use our Bulk Update with Entity Framework is to **update their data exactly the way they want** — whether that means updating only specific columns, skipping `null` values, or applying conditional logic. And of course, all that with great performance.

- ✅ **Update the way you want:** Choose which properties to update, skip `null` values, apply conditions, and more.
- ✅ **Extremely fast:** Update thousands or millions of rows in seconds.
- ✅ **No need to load entities:** Save time and memory by skipping entity tracking.
- ✅ **Flexible with hundreds of options:** Fine-tune everything — batch size, conditions, behaviors — to match your business logic.

## 🔍 What is supported?

Our library supports all the common scenarios — and almost everything you can do with EF Core and EF6!

- ✅ The latest Entity Framework Core version: EF Core 9  
- ✅ All previous EF Core versions: EF Core 2 to 8  
- ✅ All Entity Framework versions: EF6, EF5, EF4, and EF Classic  
- ✅ All major database providers: SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle  
- ✅ All inheritance mapping strategies: TPC, TPH, and TPT  
- ✅ Complex types / owned entity types  
- ✅ Enums  
- ✅ Value converters (EF Core)  
- ✅ And much more — even shadow properties!

### 🚀 Performance Comparison

A key benefit of our library is the performance it provides — but also the fact that you **don’t need to retrieve entities from the database**, unlike what you usually do with `SaveChanges`.

With our `BulkUpdate`, you can just **create the entities yourself** and **set only the properties you want to update**. That’s it!

For fairness, our online benchmark **also includes retrieving entities from the database**, so the comparison remains honest and realistic.

### EF Core vs EFE

| Operation                           | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :---------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                         | 400 ms         | 725 ms         | 1420 ms        |
| BulkUpdate                          | 115 ms         | 200 ms         | 350 ms         |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/sqcYkC)

In other words, to update 5,000 entities, our `BulkUpdate` is about **4x faster**, reducing update time by **75%**.

If you [Include your Graph](https://entityframework-extensions.net/include-graph), the performance difference will be even greater — while only using a **small memory footprint**.

Learn more about it in our section on [Memory & Performance Improvements](https://entityframework-extensions.net/v7-100-0-0-include-graph#memory-performance-improvements).


### EF6 vs EFE

In EF6, the `SaveChanges` method sends one database round-trip for every single entity it needs to update. So if you're dealing with hundreds or thousands of entities, you're in trouble. Back then, our library was the only real solution — there was simply no competition. If you wanted performance, you had to use us. Otherwise, your users were stuck waiting forever.

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkUpdate      | 100 ms         | 120 ms         | 170 ms         |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/xVwYDE)

In other words, to update 5,000 entities, our `BulkUpdate` is about **30x faster**, reducing update time by **97%**.

## Real Life Scenarios

### Update and include/exclude properties
You want to update your entities but only for specific properties.

- `IgnoreOnUpdateExpression`: This option lets you ignore properties that are auto-mapped.

```csharp  
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;
          
context.BulkUpdate(customers, options => options.IgnoreOnUpdateExpression = c => new { c.ColumnToIgnore } );
```

[Try it in EF Core](https://dotnetfiddle.net/Enr2KP) | [Try it in EF6](https://dotnetfiddle.net/R43wS0)

### Update with custom key
You want to update entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` lets you use as a key any property or combination of properties.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code);    
```

[Try it in EF Core](https://dotnetfiddle.net/YasxiY) | [Try it in EF6](https://dotnetfiddle.net/La7vr8)

### Update with related child entities (Include Graph)
You want to update entities but also automatically insert related child entities.

- `IncludeGraph`: This option lets you automatically update all entities part of the graph.
- `IncludeGraphBuilder`: This option lets you customize how to update entities for a specific type.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkUpdate(invoices, options => options.IncludeGraph = true);
```

[Try it in EF Core](https://dotnetfiddle.net/Iciz2K) | [Try it in EF6](https://dotnetfiddle.net/PAVo4c)

### Update with future action
You want to update entities, but you want to defer the execution.

By default, `BulkUpdate` is an immediate operation. That means, it's executed as soon as you call the method.

`FutureAction`: This option lets you defer the execution of a Bulk Update.
`ExecuteFutureAction`: This option triggers and executes all pending `FutureAction`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.FutureAction(x => x.BulkUpdate(customers));
context.FutureAction(x => x.BulkUpdate(invoices, options => options.IncludeGraph = true));

// ...code...

context.ExecuteFutureAction();
```

[Try it in EF Core](https://dotnetfiddle.net/i9pMsP) | [Try it in EF6](https://dotnetfiddle.net/YnV5Fs)

## Bulk Update Options

### Configuring Options

We already saw in previous article [Configuring Options](/configure-options) how to pass options to the `BulkUpdate` method — but here’s a quick recap:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a lambda expression (only works with one option)
context.BulkUpdate(list, options => options.IncludeGraph = true);

// Using a lambda expression with a body (works with one or multiple options)
context.BulkUpdate(list, options =>
{
    options.IncludeGraph = true;
    options.ColumnPrimaryKeyExpression = x => new { x.ID };
});

// Using a `BulkOperationOption` instance
var options = context.CreateBulkOptions<EntitySimple>();
options.IncludeGraph = true;
options.ColumnPrimaryKeyExpression = x => new { x.ID };

context.BulkUpdate(list, options);
```

> 💡 Tip: Using a `BulkOperationOption` instance is useful when you want to reuse the same configuration across multiple operations or keep your setup code more organized.

### Common Options

- Bulk Update Behavior
   - **UpdatePrimaryKeyAndFormula:** Specify a hardcoded SQL to include additional logic—along with the primary key—to check if the entity matches an existing row in the database. Only rows that also match the formula will be updated.
   - **UpdateStagingTableFilterFormula:** Specify a hardcoded SQL if you want to filter which rows should be updated using a staging table.
- Coalesce Behavior
   - **OnUpdateUseCoalesce:** For each property, during the update, if the source value is `null`, the destination value will stay unchanged. This behaves like `ISNULL(StagingTable.ColumnName, DestinationTable.ColumnName)` in SQL Server.
   - **OnUpdateUseCoalesceDestination:** For each property, during the update, the destination value will only be updated if its current value in the database is `null`. This behaves like `ISNULL(DestinationTable.ColumnName, StagingTable.ColumnName)` in SQL Server.
   - **CoalesceOnUpdateExpression:** Use a lambda expression to specify which properties should apply the `OnUpdateUseCoalesce` logic during the update.
   - **CoalesceOnUpdateNames:** Use a list of strings to specify which properties should apply the `OnUpdateUseCoalesce` logic during the update.
   - **CoalesceDestinationOnUpdateExpression:** Use a lambda expression to specify which properties should apply the `OnUpdateUseCoalesceDestination` logic during the update.
   - **CoalesceDestinationOnUpdateNames:** Use a list of strings to specify which properties should apply the `OnUpdateUseCoalesceDestination` logic during the update.
- Matched Behavior
   - **UpdateMatchedAndFormula:** After matching rows by primary key, you can specify an additional SQL condition to update only the rows that also satisfy this formula.
   - **UpdateMatchedAndConditionExpression:**  After matching rows by primary key, you can specify additional properties using a lambda expression. All specified property values must match between the entity and the database for the row to be updated.
   - **UpdateMatchedAndConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. All specified property values must match between the entity and the database for the row to be updated.
   - **UpdateMatchedAndOneNotConditionExpression:** After matching rows by primary key, you can specify additional properties using a lambda expression. At least one of the specified property values must differ between the entity and the database for the row to be updated.
   - **UpdateMatchedAndOneNotConditionNames:** After matching rows by primary key, you can specify additional properties using a list of strings. At least one of the specified property values must differ between the entity and the database for the row to be updated.
   - **IgnoreOnUpdateMatchedAndConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `UpdateMatchedAndConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnUpdateMatchedAndConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `UpdateMatchedAndConditionNames`, and all other properties will be used for the match.
   - **IgnoreOnUpdateMatchedAndOneNotConditionExpression:** Use a lambda expression to select the properties you want to ignore. These properties will be excluded from the comparison performed by `UpdateMatchedAndOneNotConditionExpression`, and all other properties will be used for the match.
   - **IgnoreOnUpdateMatchedAndOneNotConditionNames:** Use a list of strings to select the properties you want to ignore. These properties will be excluded from the comparison performed by `UpdateMatchedAndOneNotConditionNames`, and all other properties will be used for the match.
- Behavior
   - **AutoTruncate:** Set to `true` if you want string values to be automatically truncated to match the maximum database length before being inserted. This option is especially useful because `SqlCommand` and `SqlBulkCopy` can behave differently when a string is too long. (See [Issue #333](https://github.com/zzzprojects/EntityFramework-Extensions/issues/333#issuecomment-1041494634))
   - **IncludeGraph:** Set to `true` if you want to update both the main entities and their related entities. For example, if you pass a list of `Order` that includes `OrderItem`, both will be updated. Be careful: if you want to apply specific options to a related entity type, you’ll need to configure them using `IncludeGraphBuilder`.
   - **IncludeGraphBuilder:** Required only if `IncludeGraph = true` **and** you need to customize how a related entity type is updated. Use a lambda expression to control how each entity in the graph should be updated — for example, to define how child entities are linked to their parent or how IDs should be propagated.
- Properties & Columns
   - **ColumnInputExpression:** Choose which properties should be updated by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputNames:** Choose which properties should be updated by using a list of strings to select them. All other properties will be ignored.
   - **ColumnInputOutputExpression:** Choose which properties should be updated **and** outputted by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputOutputNames:** Choose which properties should be updated **and** outputted by using a list of strings to select them. All other properties will be ignored.
   - **ColumnOutputExpression:** Choose which properties should be outputted after the update by using a lambda expression to select them.
   - **ColumnOutputNames:** Choose which properties should be outputted after the update by using a lambda expression to select them.
   - **ColumnPrimaryKeyExpression:** Choose which properties should be part of the key by using a lambda expression. Only rows that match the key will be updated.
   - **ColumnPrimaryKeyNames:** Choose which properties should be part of the key by using a list of strings. Only rows that match the key will be updated.
   - **IgnoreOnUpdateExpression:** Choose which properties should be ignored by using a lambda expression to select them. All other properties will be updated.
   - **IgnoreOnUpdateNames:** Choose which properties should be ignored by using a list of strings to select them. All other properties will be updated.
- Optimization
   - **Batch:** Customize the `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` to improve performance and control how updated entities are grouped and executed.
   - **Hint:** Use `QueryHint` or `TableHintSql` to apply SQL hints for additional performance tuning.
   - **UseTableLock:** Set to `true` to lock the destination table during the update operation, which can improve performance by reducing row-level locks and avoiding lock escalation. This is especially useful when inserting a large number of rows.
- General
   - **Audit:** Track updated entities by using the `UseAudit` and `AuditEntries` options. [Learn more here](/audit)
   - **FutureAction:** Batch multiple update operations and execute them later using the `ExecuteFuture` or `ExecuteFutureAsync` methods.
   - **Log:** Log all executed SQL statements using the `Log`, `UseLogDump`, and `LogDump` options. [Learn more here](/logging)
   - **RowsAffected:** Use `UseRowsAffected = true`, then access `ResultInfo.RowsAffected` or `ResultInfo.RowsAffectedUpdated` to get the number of entities updated. [Learn more here](/rows-affected)


## Conclusion

The `BulkUpdate` method is very powerful. It allows you to customize how your entities are updated—like skipping certain properties or using a conditional formula—without sacrificing performance. In fact, it runs faster than regular EF Core updates as we saw in our benchmark.