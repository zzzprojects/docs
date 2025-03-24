---
Title: EF Core Bulk Insert | Optimize Data Insertion for EF Core and EF6
MetaDescription: Optimize Entity Framework insert performance with EF Core Bulk Insert Extensions. Easily insert large numbers of entities and customize options with compatibility across all EF versions, including EF Core 7, 6, 5, 3, and EF6. Improve your database operations - try it now.
LastMod: 2025-03-20
---

# Bulk Insert /n Boost your EF Core insert performance now

The `BulkInsert` method is the easiest way you can insert thousands of entities in EF Core. In addition to being super fast, you can also customize it with various options to insert entities the way you want—such as keeping identity values, inserting only entities that don't already exist in the database, and much more.

```csharp
// Easy to use
context.BulkInsert(customers);

// Easy to customize
context.BulkInsert(invoices, options => options.IncludeGraph = true);
```

[Online Example (EF Core)](https://dotnetfiddle.net/2eVfFT) | [Online Example (EF6)](https://dotnetfiddle.net/bNektu)

If you want to insert entities in EF Core even faster, you can use the [BulkInsertOptimized](/bulk-insert-optimized) method. The major difference between the two methods is:

- **BulkInsert:** By default, `AutoMapOutputDirection = true`, so it returns values like the identity value. This requires additional logic and can result in a less optimized SQL statement.
- **BulkInsertOptimized:** By default, `AutoMapOutputDirection = false`, so it does not return any values unless you explicitly specify otherwise.

## 🔑 Key Benefits

One of the main raison people use our library is for the performance and to reduce the memory usage. The other reason is for the flexibility that we will see later.

- ✅ Extremely fast insert
- ✅ Memory-efficient
- ✅ Flexible with hundreds of options
- ✅ Support all majors database providers
- ✅ Support the latest EF Core 9 version
- ✅ Support all legacy version of EF Core (2-8), EF5, and EF6

## 🚀 Performance Comparison

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,200 ms       | 2,400 ms       | 6,000 ms       |
| BulkInsert      | 50 ms          | 55 ms          | 75 ms          |

[Try it in EF Core](https://dotnetfiddle.net/ttbri7) | [Try it in EF6](https://dotnetfiddle.net/pSpD10)

### What is supported?
- All Entity Framework Core Version: EF Core 9, EF Core 8, EF Core 7, EF Core 6, EF Core 5, EF Core 3
- All Entity Framework Version: EF6, EF5, EF4
- All Inheritances (TPC, TPH, TPT)
- Complex Type/Owned Entity Type
- Enum
- Value Converter (EF Core)
- And more!

## Getting Started

### Bulk Insert
The `BulkInsert` and `BulkInsertAync` methods extend your `DbContext` to let you insert a large number of entities in your database.

```csharp
context.BulkInsert(customers);

context.BulkInsertAsync(customers, cancellationToken);
```

[Try it in EF Core](https://dotnetfiddle.net/54yNvX) | [Try it in EF6](https://dotnetfiddle.net/4xB7Tf)

### Bulk Insert with options
The `options` parameter let you use a lambda expression to customize the way entities are inserted.

```csharp
context.BulkInsert(customers, options => options.BatchSize = 100);

context.BulkInsert(customers, options => { 
    options.InsertIfNotExists = true;
    options.PrimaryKeyExpression = customer => customer.Code;
  });
```

[Try it in EF Core](https://dotnetfiddle.net/jpQVna) | [Try it in EF6](https://dotnetfiddle.net/9rUgry)

### Why BulkInsert is faster than SaveChanges?
Inserting thousands of entities for an initial load or a file importation is a typical scenario.

The `SaveChanges` method makes it quite slow/impossible to handle this kind of situation due to the number of database round-trips required. The `SaveChanges` perform one database round-trip for every entity to insert. So, if you need to insert 10,000 entities, 10,000 database round-trips will be performed which is **INSANELY** slow.

The `BulkInsert` in counterpart requires the minimum number of database round-trips possible. For example, under the hood for SQL Server, a `SqlBulkCopy` is performed to insert 10,000 entities which is the fastest way available.

## Real Life Scenarios

### Insert and keep identity value
Your entity has an identity property, but you want to force to insert a specific value instead. The `InsertKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
context.BulkInsert(customers, options => options.InsertKeepIdentity = true);
```

[Try it in EF Core](https://dotnetfiddle.net/iZULeX) | [Try it in EF6](https://dotnetfiddle.net/JJeqbn)

### Insert and include/exclude properties
You want to insert your entities but only for specific properties.

- `ColumnInputExpression`: This option let you choose which properties to map.
- `IgnoreOnInsertExpression`: This option let you ignore properties that are auto-mapped.

```csharp
context.BulkInsert(customers, options => options.ColumnInputExpression = c => new { c.CustomerID, c.Name} );
            
context.BulkInsert(customers, options => options.IgnoreOnInsertExpression = c => new { c.ColumnToIgnore } );
```

[Try it in EF Core](https://dotnetfiddle.net/B32EVO) | [Try it in EF6](https://dotnetfiddle.net/TUyT7A)

### Insert only if the entity does not already exists
You want to insert entities but only those that don't already exist in the database.

- `InsertIfNotExists`: This option let you insert only entities that doesn't already exists.
- `PrimaryKeyExpression`: This option let you customize the key to use to check if the entity already exists or not.

```csharp
context.BulkInsert(customers, options => {
    options.InsertIfNotExists = true;
    options.ColumnPrimaryKeyExpression = c => c.Code;
});
```

[Try it in EF Core](https://dotnetfiddle.net/bNmy6f) | [Try it in EF6](https://dotnetfiddle.net/Etc2k7)

### Insert with related child entities (Include Graph)
You want to insert entities but also automatically insert related child entities.

- `IncludeGraph`: This option lets you to automatically insert all entities part of the graph.
- `IncludeGraphBuilder`: This option lets you customize how to insert entities for a specific type.

```csharp
context.BulkInsert(invoices, options => options.IncludeGraph = true);
```

[Try it in EF Core](https://dotnetfiddle.net/Nd52w2) | [Try it in EF6](https://dotnetfiddle.net/geInHT)

### Insert with future action
You want to insert entities, but you want to defer the execution.

By default, `BulkInsert` is an immediate operation. That means, it's executed as soon as you call the method.

`FutureAction`: This option let you defer the execution of a Bulk Insert.
`ExecuteFutureAction`: This option trigger and execute all pending `FutureAction`.

```csharp
context.FutureAction(x => x.BulkInsert(customers));
context.FutureAction(x => x.BulkInsert(invoices, options => options.IncludeGraph = true));

// ...code...

context.ExecuteFutureAction();
```

[Try it in EF Core](https://dotnetfiddle.net/ltH0a4) | [Try it in EF6](https://dotnetfiddle.net/Ju3ReL)

### Insert without returning identity value
By default, the EF `BulkInsert` method already returns the identity when inserting.

However, such behavior impacts performance. For example, when the identity must be returned, a temporary table is created in SQL Server instead of directly using `SqlBulkCopy` into the destination table.

You can improve your performance by turning off the `AutoMapOutput` option.

```csharp
context.BulkInsert(customers, options => options.AutoMapOutputDirection = false);
```

[Try it in EF Core](https://dotnetfiddle.net/HnG2oq) | [Try it](https://dotnetfiddle.net/Ld0l03)

### More scenarios
Hundreds of scenarios have been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkInsert

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `BulkInsert<T>(items)` | Bulk insert entities in your database. | [EFCore](https://dotnetfiddle.net/n0p2IF) / [EF6](https://dotnetfiddle.net/ShIYXu) |
| `BulkInsert<T>(items, options)` | Bulk insert entities in your database.  | [EFCore](https://dotnetfiddle.net/09b2zo) / [EF6](https://dotnetfiddle.net/NBVSTZ) |
| `BulkInsertAsync<T>(items)` | Bulk insert entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/uYPaIS) / [EF6](https://dotnetfiddle.net/v7621p) |
| `BulkInsertAsync<T>(items, options)` | Bulk insert entities asynchronously in your database.  | [EFCore](https://dotnetfiddle.net/eWkJco) / [EF6](https://dotnetfiddle.net/wuFZDZ) |
| `BulkInsertAsync<T>(items, cancellationToken)` | Bulk insert entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/4iW0p6) / [EF6](https://dotnetfiddle.net/qrDSny) |
| `BulkInsertAsync<T>(items, options, cancellationToken)` | Bulk insert entities asynchronously in your database. | [EFCore](https://dotnetfiddle.net/VkmKH2) / [EF6](https://dotnetfiddle.net/iPmJw9) |

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

## Limitations

### Hidden Navigation (EF6 only)

The `BulkInsert` doesn't use the ChangeTracker to optimize the performance unless no other alternative exists.

For example, you want to insert `InvoiceItem` but there is no relation toward the parent `Invoice`. In this case, you will need to add your entities in the `ChangeTracker`. The `ChangeTracker` will be used to find the related `Invoice` for your `InvoiceItem`.

```csharp
try
{
    context.BulkInsert(items);
}
catch
{
    Console.WriteLine("An error is throw because the Invoice relation cannot be found.");
}

context.Invoices.AddRange(invoices);

// The ChangeTracker is used for this case
context.BulkInsert(items);
```

[Try it in EF6](https://dotnetfiddle.net/vKlII0)


---


---


There is various way to use the Bulk Insert method:

- You can use the `IncludeGraph = true` option to insert an entire object graph (for example, an Invoice and its associated InvoiceItem)
- You can use the `AutoMapOutputDirection = false` option to get the best possible performance by not returning value that is normally returned like the identity value
- You can pass option by either a lambda expression or create a new instance of the `BulkOperationsOptions` class.

```csharp
// Easy to use
context.BulkInsert(customers);

// Easy to customize
context.BulkInsert(invoices, options => options.IncludeGraph = true);

// For best performance
context.BulkInsert(customers, options => options.AutoMapOutputDirection = false);

// Use options with an instance
// var options = new BulkOperationsOptions<Customer>();
var options = context.CreateBulkOptions(customers);
context.BulkInsert(customers, options);
```

[Try it in EF Core](https://dotnetfiddle.net/2eVfFT) | [Try it in EF6](https://dotnetfiddle.net/bNektu)
