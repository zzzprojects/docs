---
Title: EF Core Bulk Delete | Optimize Data Deletion for EF6 and EF Core
MetaDescription: Efficiently delete Entity Framework data with EF Core Bulk Delete Extensions. Customize options to quickly delete large numbers of entities with ease, compatible with all EF versions including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2025-03-18
---

# Bulk Delete /n Swiftly perform delete operations on thousands of entities in EF Core

The `BulkDelete` method lets you delete thousands of entities in EF Core. The biggest advantage of this method over the traditional approach is that you don’t need to fetch your entities from the database before deleting them (which doesn’t make much sense since you’re deleting them!).

```csharp
// Easy to use
context.BulkDelete(customers);

// Easy to customize
context.BulkDelete(customers, options => options.BatchSize = 100);
```

[Online Example (EF Core)](https://dotnetfiddle.net/BCyXU6) | [Online Example (EF6)](https://dotnetfiddle.net/ESKZJq)

Our library also offers several other ways to delete your entities even more easily and quickly:
- [Delete from Query](/delete-from-query)
- [Delete by Key](/delete-by-key)
- [Delete Range by Key](/delete-range-by-key)

### Performance Comparison

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,200 ms       | 2,400 ms       | 6,000 ms       |
| BulkDelete      | 50 ms          | 55 ms          | 75 ms         |

[Try it in EF Core](https://dotnetfiddle.net/9r3vLC) | [Try it in EF6](https://dotnetfiddle.net/qYjiA9)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### Scenarios
The `BulkDelete` method is **fast** but also **flexible** to let you handle various scenarios in Entity Framework such as:

- [Delete with custom key](#delete-with-custom-key)
- [Delete with future action](#delete-with-future-action)
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

### Bulk Delete
The `BulkDelete` and `BulkDeleteAync` methods extend your `DbContext` to let you delete a large number of entities in your database.

```csharp
context.BulkDelete(customers);

context.BulkDeleteAsync(customers, cancellationToken);
```

[Try it in EF Core](https://dotnetfiddle.net/EO0Z1R) | [Try it in EF6](https://dotnetfiddle.net/10nw7a)

### Bulk Delete with options
The `options` parameter let you use a lambda expression to customize the way entities are deleted.

```csharp
context.BulkDelete(customers, options => options.BatchSize = 100);
```

[Try it in EF Core](https://dotnetfiddle.net/lIUiH2) | [Try it in EF6](https://dotnetfiddle.net/ygZVAu)

### Why BulkDelete is faster than SaveChanges?
Deleting thousands of entities for a file importation is a typical scenario.

The `SaveChanges` method makes it quite impossible to handle this kind of situation due to the number of database round-trips required. The `SaveChanges` performs one database round-trip for every entity to delete. So, if you need to delete 10,000 entities, 10,000 database round-trips will be performed which is **INSANELY** slow.

The `BulkDelete` in counterpart requires the minimum number of database round-trips possible. For example, under the hood for SQL Server, a `SqlBulkCopy` is performed first in a temporary table, then a `DELETE` from the temporary table to the destination table is performed which is the fastest way available.

## Real Life Scenarios

### Delete with custom key
You want to delete entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` let you use as a key any property or combination of properties.

```csharp
context.BulkDelete(customers, options => options.ColumnPrimaryKeyExpression = c => c.Code);    
```

[Try it in EF Core](https://dotnetfiddle.net/91wZzc) | [Try it in EF6](https://dotnetfiddle.net/9M6bKt)

### Delete with related child entities (Include Graph)
You want to delete entities but also automatically delete related child entities.

- `IncludeGraph`: This option lets you to automatically delete all entities part of the graph.
- `IncludeGraphBuilder`: This option lets you customize how to delete entities for a specific type.

```csharp
context.BulkDelete(invoices, options => options.IncludeGraph = true);
```

[Try it in EF Core](https://dotnetfiddle.net/SHM63t)

NOTE: Only support EF Core 3+

### Delete with future action
You want to delete entities, but you want to defer the execution.

By default, `BulkDelete` is an immediate operation. That means, it's executed as soon as you call the method.

`FutureAction`: This option let you defer the execution of a Bulk Delete.
`ExecuteFutureAction`: This option trigger and execute all pending `FutureAction`.

```csharp
context.FutureAction(x => x.BulkDelete(customers1));
context.FutureAction(x => x.BulkDelete(customers2));

// ...code...

context.ExecuteFutureAction();
```

[Try it in EF Core](https://dotnetfiddle.net/V6KsSl) | [Try it in EF6](https://dotnetfiddle.net/KovTrj) 

### More scenarios
Hundreds of scenarios have been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkDelete

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `BulkDelete<T>(items)` | Bulk delete entities in your database. | [EFCore](https://dotnetfiddle.net/gwc9hl) / [EF6](https://dotnetfiddle.net/4Jv1H6)|
| `BulkDelete<T>(items, options)` | Bulk delete entities in your database.  | [EFCore](https://dotnetfiddle.net/Qek2MJ) / [EF6](https://dotnetfiddle.net/IedG1h) |
| `BulkDeleteAsync<T>(items)` | Bulk delete entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/MJLo2d) / [EF6](https://dotnetfiddle.net/n5OhXL) |
| `BulkDeleteAsync<T>(items, cancellationToken)` | Bulk delete entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/rRL627) / [EF6](https://dotnetfiddle.net/RfSB6I) |
| `BulkDeleteAsync<T>(items, options, cancellationToken)` | Bulk delete entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/SZ54Px) / [EF6](https://dotnetfiddle.net/r1Hkw7) |

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
