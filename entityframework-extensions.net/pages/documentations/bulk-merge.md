---
Title: EF Core Bulk Merge | Optimize Data Upserting for EF6 and EF Core
MetaDescription: Efficiently add or update Entity Framework data with EF Core Bulk Merge Extensions. Perform upsert operations on large numbers of entities with customizable options for all EF versions, including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2025-03-19
---

# Bulk Merge /n Easily perform add or update (Upsert) operations in EF Core

The `BulkMerge` method lets you merge entities using your [EF Core model](https://www.learnentityframeworkcore.com/model) into your database. This kind of merge operation is often called “Upsert,” “Add or Update,” or “Insert or Update.” There is currently no direct equivalent to `BulkMerge` built into EF Core.

When you perform a bulk merge, it behaves as follows:
- Rows that match the entity key are **UPDATED**.
- Rows that do not match any existing record are **INSERTED**.

```csharp
// Easy to use
context.BulkMerge(customers);

// Easy to customize
context.BulkMerge(customers, options => options.IncludeGraph = true);
```

[Online Example (EF Core)](https://dotnetfiddle.net/v08Jzy) | [Online Example (EF6)](https://dotnetfiddle.net/Aodij2)

### Performance Comparison

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 4,000 ms       | Too long...     | Way way too long... |
| BulkMerge       | 80 ms          | 110 ms         | 170 ms         |

[Try it in EF Core](https://dotnetfiddle.net/hmDtiI) | [Try it in EF6](https://dotnetfiddle.net/Erk8R3)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.
### Scenarios
The `BulkMerge` method is **fast** but also **flexible** to let you handle various scenarios in Entity Framework such as:

- [Merge and keep identity value](#merge-and-keep-identity-value)
- [Merge with custom key](#merge-with-custom-key)
- [Merge and include/exclude properties](#merge-and-includeexclude-properties)
- [Merge with related child entities (Include Graph)](#merge-with-related-child-entities-include-graph)
- [Merge with future action](#merge-with-future-action)
- [More scenarios](#more-scenarios)

### What is supported?
- All Entity Framework Core Version: EF Core 9, EF Core 8, EF Core 7, EF Core 6, EF Core 5, EF Core 3
- All Entity Framework Version: EF6, EF5, EF4
- All Inheritances (TPC, TPH, TPT)
- Complex Type/Owned Entity Type
- Enum
- Value Converter (EF Core)
- And more!

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness
- Reduce database load
- Reduce database round-trips

## Getting Started

### Bulk Merge
The `BulkMerge` and `BulkMergeAync` methods extend your `DbContext` to let you merge a large number of entities in your database.

```csharp
context.BulkMerge(customers);

context.BulkMergeAsync(customers, cancellationToken);
```

[Try it in EF Core](https://dotnetfiddle.net/GqKIoc) | [Try it in EF6](https://dotnetfiddle.net/0Ba6ZB)

### Bulk Merge with options
The `options` parameter let you use a lambda expression to customize the way entities are inserted or updated.

```csharp
context.BulkMerge(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code });
```

[Try it in EF Core](https://dotnetfiddle.net/Lzbh2H) | [Try it in EF6](https://dotnetfiddle.net/JsHWWm)

### Why BulkMerge is faster than SaveChanges?
Merging thousands of entities for a file importation is a typical scenario.

The `AddOrUpdate` method performs a database round-trip for every entity to check if it already exists. The `DetectChanges` change method is also called for every entity which makes this method even slower (it's like using the `Add` method instead of `AddRange`).

The `SaveChanges` method performs one database round-trip for every entity to update.

So, if you need to merge 10,000 entities, 20,000 database round-trips will be performed + 10,000 `DetectChanges` calls which is **INSANELY** slow.

The `BulkMerge` in counterpart requires the minimum number of database round-trips possible. For example, under the hood for SQL Server, a `SqlBulkCopy` is performed first in a temporary table, then a `MERGE` from the temporary table to the destination table is performed which is the fastest way available.

## Real Life Scenarios

### Merge and keep identity value
Your entity has an identity property, but you want to force to insert a specific value instead. The `MergeKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
context.BulkMerge(customers, options => options.MergeKeepIdentity = true);
```

[Try it in EF Core](https://dotnetfiddle.net/mflJdW) | [Try it in EF6](https://dotnetfiddle.net/a1qGoh)

### Merge and include/exclude properties
You want to merge your entities but only for specific properties.

- `ColumnInputExpression`: This option lets you choose which properties to map.
- `ColumnIgnoreExpression`: This option lets you ignore properties that are auto-mapped.
- `IgnoreOnMergeInsertExpression`: This option let you ignore properties only for the `INSERT` part.
- `IgnoreOnMergeUpdateExpression`: This option let you ignore properties only for the `UPDATE` part.

```csharp
context.BulkMerge(customers, options => options.ColumnInputExpression = c => new { c.CustomerID, c.Name} );
            
context.BulkMerge(customers, options => options.IgnoreOnMergeUpdateExpression = c => new { c.UpdatedDate } );
```

[Try it in EF Core](https://dotnetfiddle.net/mogvhA) | [Try it in EF6](https://dotnetfiddle.net/l6NLDA)

### Merge with custom key
You want to merge entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` let you use as a key any property or combination of properties.

```csharp
context.BulkMerge(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code);    
```

[Try it in EF Core](https://dotnetfiddle.net/LqM4UJ) | [Try it in EF6](https://dotnetfiddle.net/cS7scF)

### Merge with related child entities (Include Graph)
You want to merge entities but also automatically merge related child entities.

- `IncludeGraph`: This option lets you automatically merge all entities part of the graph.
- `IncludeGraphBuilder`: This option lets you customize how to merge entities for a specific type.

```csharp
context.BulkMerge(invoices, options => options.IncludeGraph = true);
```

[Try it in EF Core](https://dotnetfiddle.net/imc0r5) | [Try it in EF6](https://dotnetfiddle.net/FEXkXi)

### Merge with future action
You want to merge entities, but you want to defer the execution.

By default, `BulkMerge` is an immediate operation. That mean, it's executed as soon as you call the method.

`FutureAction`: This option lets you defer the execution of a Bulk Merge.
`ExecuteFutureAction`: This option trigger and execute all pending `FutureAction`.

```csharp
context.FutureAction(x => x.BulkMerge(customers));
context.FutureAction(x => x.BulkMerge(invoices, options => options.IncludeGraph = true));

// ...code...

context.ExecuteFutureAction();
```

[Try it in EF Core](https://dotnetfiddle.net/CmZ8v3) | [Try it in EF6](https://dotnetfiddle.net/RUL0rL)

### More scenarios
Hundreds of scenarios have been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkMerge

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `BulkMerge<T>(items)` | Bulk merge entities in your database. | [EFCore](https://dotnetfiddle.net/fLB6rt) / [EF6](https://dotnetfiddle.net/gSzNDC) |
| `BulkMerge<T>(items, options)` | Bulk merge entities in your database.  | [EFCore](https://dotnetfiddle.net/NjrdKg) / [EF6](https://dotnetfiddle.net/WZBs0E) |
| `BulkMergeAsync<T>(items)` | Bulk merge entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/wFA1C9) / [EF6](https://dotnetfiddle.net/PXCsCp) |
| `BulkMergeAsync<T>(items, cancellationToken)` | Bulk merge entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/sKHwNu) / [EF6](https://dotnetfiddle.net/WZTGVc) |
| `BulkMergeAsync<T>(items, options, cancellationToken)` | Bulk merge entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/tG7rCT) / [EF6](https://dotnetfiddle.net/oQjJsY) |

###### Options
More options can be found here:

- [Audit](https://entityframework-extensions.net/audit)
- [Batch](https://entityframework-extensions.net/batch)
- [Column](https://entityframework-extensions.net/column)
- [Context Factory](https://entityframework-extensions.net/context-factory)
- [Execute Event](https://entityframework-extensions.net/execute-event)
- [Identity](https://entityframework-extensions.net/identity)
- [Include Graph](https://entityframework-extensions.net/include-graph)
- [Key](https://entityframework-extensions.net/key)
- [Logging](https://entityframework-extensions.net/logging)
- [Temporary Table](https://entityframework-extensions.net/temporary-table)
- [Transaction](https://entityframework-extensions.net/transaction)
- [Transient Error](https://entityframework-extensions.net/transient-error)
- [SQL Server](https://entityframework-extensions.net/sql-server)
