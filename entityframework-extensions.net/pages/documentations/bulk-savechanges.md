---
Title: EF Core Bulk SaveChanges | Optimize Data Saving for EF6 and EF Core
MetaDescription: Efficiently save Entity Framework changes with EF Core Bulk SaveChanges Extensions. Quickly save large numbers of changes with customizable options for all EF versions, including EF Core 7, 6, 5, 3, and EF6. Optimize your database operations - try it now.
LastMod: 2025-07-28
---

# Bulk SaveChanges /n Supercharge your EF Core SaveChanges by adding 'Bulk'

The `BulkSaveChanges` method works like EF Core’s [SaveChanges](https://learn.microsoft.com/en-us/ef/core/saving/basic) method—only faster! It saves to the database all entities in your [ChangeTracker](https://www.learnentityframeworkcore.com/dbcontext/change-tracker) that have one of the following states: **Added**, **Modified**, or **Deleted**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkSaveChanges();

// Easy to customize
context.BulkSaveChanges(options => options.BatchSize = 100);
```

[Online Example (EF Core)](https://dotnetfiddle.net/4nbecz) | [Online Example (EF6)](https://dotnetfiddle.net/MP65WH)

💡 **One key difference:** If you provide an explicit identity value, you still need to use the `InsertKeepIdentity = true` option to tell our library to keep it.

### Performance Comparison

| Operations       | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :--------------- | -------------: | -------------: | -------------: |
| SaveChanges      | 1,200 ms       | 2,400 ms       | 6,000 ms       |
| BulkSaveChanges  | 150 ms         | 225 ms         | 500 ms         |

[Try it in EF Core](https://dotnetfiddle.net/Oshg3S) | [Try it in EF6](https://dotnetfiddle.net/4FLmNE)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

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

### FAQ

#### Why BulkSaveChanges is faster than SaveChanges?
The `SaveChanges` method makes it quite slow/impossible to handle a scenario that requires to save a lot of entities due to the number of database round-trips required. The `SaveChanges` performs one database round-trip for every entity to insert. So, if you need to insert 10,000 entities, 10,000 database round-trips will be performed which is **INSANELY** slow.

The `BulkSaveChanges` in counterpart requires the minimum number of database round-trips possible. By using Bulk Operations, fewer commands are executed which lead to better performance.

### Why BulkSaveChanges update all columns by default (EF6 only)?
The `BulkSaveChanges` tries to combine most commands in a single operation. Updating different columns from an action to another will require generating different SQL and being executed in several database round-trips.

That's still possible to have the same behavior of `SaveChanges` by disabling the option `ForceUpdateUnmodifiedValues`:

```csharp
ctx.BulkSaveChanges(options => options.ForceUpdateUnmodifiedValues = false);
```

For `EF Core`, this option is not available. Only columns that have a modification will be saved exactly like `SaveChanges`.

#### When should I use BulkSaveChanges over SaveChanges?
Whenever you have more than one entity to save. The `BulkSaveChanges` is almost as fast as the `SaveChanges` for one entity, but becomes way faster as the number of entities to save grows.

#### When should I use BulkSaveChanges over BatchSaveChanges?
`BatchSaveChanges` become slower and slower in comparison to `BulkSaveChanges` when the number of entities to save grows due to the `ChangeTracker`.

After a few thousands of entities, we recommend using `BulkSaveChanges` which is a more scalable solution.

## Documentation

### BatchSaveChanges

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `BulkSaveChanges()` | Save all changes made in this context to the underlying database by executing bulk operations. | [EFCore](https://dotnetfiddle.net/s0NxkN) / [EF6](https://dotnetfiddle.net/nKd0mT) |
| `BulkSaveChanges(Action<BulkOperation> bulkOperationFactory)` | Save all changes made in this context to the underlying database by executing bulk operations. | [EFCore](https://dotnetfiddle.net/yd4iaz) / [EF6](https://dotnetfiddle.net/lJVdXR) |
| `BulkSaveChanges(bool useEntityFrameworkPropagation)` | Save all changes made in this context to the underlying database by executing bulk operations. | [EFCore](https://dotnetfiddle.net/rQS1u5) / [EF6](https://dotnetfiddle.net/ZWNQPA) |
| `BulkSaveChanges(bool useEntityFrameworkPropagation, Action<BulkOperation> bulkOperationFactory)` | Save all changes made in this context to the underlying database by executing bulk operations. | [EFCore](https://dotnetfiddle.net/FJ4dYY) / [EF6](https://dotnetfiddle.net/Aqp0EK) |
| `BulkSaveChangesAsync()` | Save all changes asynchronously made in this context to the underlying database by executing bulk operations. | [EFCore](https://dotnetfiddle.net/ZZmHPK) / [EF6](https://dotnetfiddle.net/7kVnBm) |
| `BulkSaveChangesAsync(Action<BulkOperation> bulkOperationFactory)` | Save all changes asynchronously made in this context to the underlying database by executing bulk operations. | [EFCore](https://dotnetfiddle.net/1oaxd1) / [EF6](https://dotnetfiddle.net/57KsGn) |
| `BulkSaveChangesAsync(bool useEntityFrameworkPropagation)` | Save all changes asynchronously made in this context to the underlying database by executing bulk operations. | [EF6](https://dotnetfiddle.net/uT7k8i) |
| `BulkSaveChangesAsync(bool useEntityFrameworkPropagation, Action<BulkOperation> bulkOperationFactory)` | Save all changes asynchronously made in this context to the underlying database by executing bulk operations. | [EF6](https://dotnetfiddle.net/Nzyi7N) |

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
