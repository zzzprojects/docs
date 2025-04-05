---
Title: Bulk Insert in EF Core / EF6 | The Fastest Way to Insert Entities
MetaDescription: Boost your EF Core inserts performance by up to 15x, reducing insert time by 94% with EF Extensions. Use BulkInsert to handle thousands of entities with less memory and more control. Fully supports EF Core 9 to 2 and EF6. Try the live benchmark now!
LastMod: 2025-04-03
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

One of the main reasons people use our Bulk Insert with Entity Framework is for its performance and reduced memory usage. Another key reason is its flexibility, with hundreds of supported options — which we’ll explore later.

- ✅ **Extremely fast insert:** Insert millions of rows in seconds instead of minutes.
- ✅ **Memory-efficient:** Keep your memory usage low, even with large datasets.
- ✅ **Flexible with hundreds of options:** Customize the behavior to fit your exact needs.


## 🔍 What is supported?

Our library supports all the common scenarios — and almost everything you can do with Entity Framework!

- ✅ The latest Entity Framework Core version: EF Core 9  
- ✅ All previous EF Core versions: EF Core 2 to 8  
- ✅ All Entity Framework versions: EF6, EF5, EF4, and EF Classic  
- ✅ All major database providers: SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle  
- ✅ All inheritance mapping strategies: TPC, TPH, and TPT  
- ✅ Complex types / owned entity types  
- ✅ Enums  
- ✅ Value converters (EF Core)  
- ✅ And much more — even shadow properties!

## 🚀 Performance Comparison

A very popular search on Google is **"Fastest way to Bulk Insert in EF Core"**—and that’s exactly what our library delivers!

Don't just take our word for it or blindly trust what we say. Instead, try it yourself using our online benchmark and see the results with a single click!

### EF Core vs EFE

The `SaveChanges` method in EF Core is much faster than it was back in the EF6 days when inserting data. Why the improvement? The EF Core team introduced a new approach using a `MERGE` statement with a `RETURNING` clause—similar to the one we’ve been using for SQL Server since 2014. So yes, we were already doing something right!

Even with this new strategy, our library is still faster. That’s because we use `SqlBulkCopy` and have deeply optimized how data is handled behind the scenes. In fact, the performance gap becomes even more noticeable when your entities have more than just a few properties. If your entity only has 2 or 3 properties—like a very simple `Customer` with just `Id`, `Name`, and `Email`—the difference might seem smaller. But once you deal with real-world entities containing 10, 15, or more properties, **our optimized approach truly shines**.


| Operation                           | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :---------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                         | 325 ms         | 575 ms         | 1,400 ms       |
| BulkInsert (Outputting values)      | 60 ms          | 90 ms          | 150 ms         |
| BulkInsert (Not Outputting values)  | 30 ms          | 50 ms          | 90 ms          |
| BulkInsertOptimized                 | 30 ms          | 50 ms          | 90 ms          |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/ttbri7)

In other words, to save 5,000 entities:
- **BulkInsert (Outputting values)** is about **9x faster**, reducing insert time by **89%**.
- **BulkInsert (Not Outputting values)** is about **15x faster**, reducing insert time by **94%**.

Our library provides the best performance when no data needs to be returned/outputted. That’s why we introduced the `AutoMapOutputDirection = false` option and the [BulkInsertOptimized](/bulk-insert-optimized) method.

### EF Core vs EFE + Include Graph

Another important benchmark for EF Core is when inserting data that includes a graph of related entities. Being faster is one big advantage we offer—but just as important, our library uses only a **fraction of the memory**.

For example, when working with millions of entities, EF Core might use up to **2,000 MB**, while our library needs only around **400 MB**. That’s a huge difference, especially in memory-constrained environments.

In this benchmark, each **Order** entity includes **5 OrderItems**. We use `IncludeGraph` to automatically handle related entities during the insert—so you don’t need to manually insert children or worry about setting their foreign keys.

| Operation                           | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :---------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                         | 1,475 ms       | 2,600 ms       | 6,500 ms       |
| BulkInsert + IncludeGraph           | 260 ms         | 450 ms         | 900 ms         |
| BulkInsertOptimized + IncludeGraph  | 200 ms         | 350 ms         | 800 ms         |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/xD6Bxk)

⚠️ On .NET Fiddle, you won’t be able to run `SaveChanges` with more than around **1,800 entities** before it crashes due to memory limits. But if you **comment out** the `SaveChanges` call, you’ll see that our library handles **5,000 entities** just fine. This helps prove an important point: performance isn’t only about speed—**memory usage matters too**.

### EF6 vs EFE

In EF6, the `SaveChanges` method makes one database round-trip for every entity it needs to insert. If you have hundreds or thousands of entities, our library is a must-have for this version. Otherwise, you're making your users wait forever for the save to complete.

| Operation                            | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :----------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                          | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert (Outputting values)       | 90 ms          | 115 ms         | 150 ms         |
| BulkInsert (Not Outputting values)   | 30 ms          | 35 ms          | 60 ms          |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/pSpD10)

In other words, to save 5,000 entities:
- **BulkInsert (Outputting values)** is about **30x faster**, reducing insert time by **97%**.
- **BulkInsert (Not Outputting values)** is about **85x faster**, reducing insert time by **99%**.

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
