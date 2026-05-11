---
title: Adding data via the DbSet
description: An examination of the methods and approaches available for adding data via the Entity Framework Core DbSet API 
canonical: /saving/adding-data
status: Published
lastmod: 2026-05-11
---

# EF Core Adding Data

`Add()` and `AddRange()` start tracking new entities so they can be inserted into the database when [`SaveChangesAsync()`](/saving/save-changes) is called.

It is useful when you want to insert one or more new rows using the normal EF Core [change-tracking workflow](/saving/change-tracker).

## Add One Entity

Most of the time, you will combine `Add()` with `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
};

context.Products.Add(product);

await context.SaveChangesAsync();
```

This inserts one new product in the database.

## Add Multiple Entities with AddRange

Use `AddRange()` when you want to add multiple new entities in one call.

```csharp
using var context = new AppDbContext();

var keyboard = new Product { Name = "Mechanical Keyboard", Price = 120 };
var mouse = new Product { Name = "Wireless Mouse", Price = 40 };
var monitor = new Product { Name = "27-inch Monitor", Price = 250 };

context.Products.AddRange(keyboard, mouse, monitor);

await context.SaveChangesAsync();
```

This inserts the three products in the database.

## Add Related Entities

EF Core can also insert related entities when they are part of the same object graph.

```csharp
using var context = new AppDbContext();

var category = new Category
{
    Name = "Peripherals",
    Products = new List<Product>
    {
        new Product { Name = "Mechanical Keyboard", Price = 120 },
        new Product { Name = "Wireless Mouse", Price = 40 },
        new Product { Name = "27-inch Monitor", Price = 250 }
    }
};

context.Categories.Add(category);

await context.SaveChangesAsync();
```

This inserts the category and the related products.

## Add and AddRange Methods

EF Core provides several methods for adding new entities to the `DbSet`.

The most common ones are:

- `Add<TEntity>(TEntity entity)`
- `AddRange<TEntity>(IEnumerable<TEntity> entities)`
- `AddRange<TEntity>(params TEntity[] entities)`

In most cases, you do not need to specify the generic type explicitly.

```csharp
context.Products.Add(product);
```

The C# compiler can infer the entity type from the object you pass. For more information, see the C# documentation on [generic methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generic-methods?redirectedfrom=MSDN).

Use `Add()` when you want to add one entity.

Use `AddRange()` when you want to add multiple entities as part of the same unit of work.

```csharp
context.Products.AddRange(keyboard, mouse, monitor);
```

Both methods use the normal EF Core change-tracking workflow. The entities are tracked first, and the database inserts are sent when `SaveChangesAsync()` runs.

For more details about range methods such as `AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`, see the official EF Core documentation on [range methods](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange).

## Important Behavior

Adding data with EF Core follows the normal `ChangeTracker` and `SaveChangesAsync()` workflow.

That means:

- `Add()` does not insert the row immediately
- `AddRange()` does not insert rows immediately
- the entities are tracked as `Added`
- `SaveChangesAsync()` sends the `INSERT` statements to the database
- generated key values are updated on the entity instances after saving
- related entities can also be inserted when they are part of the same object graph

For a deeper explanation of how tracked changes are persisted, see [SaveChanges](/saving/save-changes).

For more about how EF Core stores entity states, see [ChangeTracker](/saving/change-tracker).

## How Adding Data Works

When you add a new entity, EF Core starts tracking it in the `Added` state.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
};

context.Products.Add(product);
```

At this point, the entity exists only in memory. No row has been inserted in the database yet.

When `SaveChangesAsync()` runs, EF Core finds all tracked entities in the `Added` state and generates the required `INSERT` statements.

```csharp
await context.SaveChangesAsync();
```

After the insert succeeds, EF Core updates the entity state and stores generated database values, such as identity keys, back on the entity instance.

For example, if the database generates the product key, you can read it after `SaveChangesAsync()` completes.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
};

context.Products.Add(product);

await context.SaveChangesAsync();

var generatedProductId = product.Id;
```

A simplified flow looks like this:

1. You create a new entity instance.
2. You call `Add()` or `AddRange()`.
3. EF Core tracks the entity as `Added`.
4. You call `SaveChangesAsync()`.
5. EF Core sends the `INSERT` statement to the database.
6. The database generates values such as identity keys.
7. EF Core updates the entity instance with those generated values.

For a deeper look at how EF Core tracks entity states, see [Tracking Changes of Entities](/saving/change-tracker-how-it-works).

## Add Related Entities and Object Graphs

EF Core can insert related entities when they are reachable from the entity being added.

For example, if a `Category` contains new `Product` entities through the `Products` navigation property, adding the category also adds the products.

```csharp
using var context = new AppDbContext();

var category = new Category
{
    Name = "Peripherals",
    Products = new List<Product>
    {
        new Product { Name = "Mechanical Keyboard", Price = 120 },
        new Product { Name = "Wireless Mouse", Price = 40 },
        new Product { Name = "27-inch Monitor", Price = 250 }
    }
};

context.Categories.Add(category);

await context.SaveChangesAsync();
```

EF Core tracks the full object graph:

- the `Category` is marked as `Added`
- each related `Product` is marked as `Added`
- the category row is inserted first
- the generated category key is used for the related product rows

This is useful when you want to create a principal entity and its related dependent entities in the same save operation.

However, EF Core only follows relationships that are actually connected in the object graph.

For example, this code creates a separate product object in memory, but that product is not connected to the category being added.

```csharp
using var context = new AppDbContext();

var category = new Category
{
    Name = "Peripherals"
};

var product = new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
};

context.Categories.Add(category);

await context.SaveChangesAsync();
```

In this case, only the category is reachable from the graph passed to `Add()`, so only the category is added.

A clearer pattern is to connect the product through the navigation property.

```csharp
using var context = new AppDbContext();

var category = new Category
{
    Name = "Peripherals",
    Products = new List<Product>
    {
        new Product { Name = "Mechanical Keyboard", Price = 120 }
    }
};

context.Categories.Add(category);

await context.SaveChangesAsync();
```

For tracked relationship scenarios, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

## AddRange is Not BulkInsert

`AddRange()` is useful when you want to add multiple entities to the `ChangeTracker` in one call.

However, `AddRange()` is not the same as a real bulk insert.

```csharp
using var context = new AppDbContext();

var keyboard = new Product { Name = "Mechanical Keyboard", Price = 120 };
var mouse = new Product { Name = "Wireless Mouse", Price = 40 };
var monitor = new Product { Name = "27-inch Monitor", Price = 250 };

context.Products.AddRange(keyboard, mouse, monitor);

await context.SaveChangesAsync();
```

This still uses the normal EF Core workflow:

1. entities are tracked
2. entities are marked as `Added`
3. `SaveChangesAsync()` generates the database commands
4. EF Core sends the inserts to the database

EF Core can batch multiple commands to reduce database round-trips, but this is still different from a dedicated bulk insert operation.

For small or moderate numbers of entities, `AddRange()` is often enough.

For thousands or millions of rows, a real bulk insert library is usually a better choice.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

using var context = new AppDbContext();

var products = GetProductsToInsert();

await context.BulkInsertAsync(products);
```

[Entity Framework Extensions BulkInsert](https://entityframework-extensions.net/bulk-insert) is designed for high-volume insert scenarios where performance matters more than the normal tracked entity workflow.

In short:

- use `Add()` for one new entity
- use `AddRange()` for multiple new entities in the normal EF Core workflow
- use `BulkInsert` when you need high-performance inserts for large datasets

## When to Use Add or AddRange

Use `Add()` or `AddRange()` when:

- you want to insert new entities using the normal EF Core workflow
- you need EF Core to track the entities before saving
- you want to insert one entity or a moderate number of entities
- you want to save related entities as part of the same object graph
- you need generated key values after `SaveChangesAsync()`
- you are already working inside a tracked unit of work

Common examples include:

- creating a new product
- adding a few records from a form
- inserting a category with related products
- inserting several entities before calling `SaveChangesAsync()` once
- combining inserts with other tracked changes in the same unit of work

For a broader overview of saving tracked changes, see [SaveChanges](/saving/save-changes).

## When Not to Use Add or AddRange

`Add()` and `AddRange()` are not always the best choice.

Avoid them when:

- you need to insert thousands or millions of rows as fast as possible
- you do not need the normal `ChangeTracker` workflow
- you are importing a large file
- you are synchronizing large datasets
- the insert operation is mainly a performance-critical batch process

For high-volume inserts, use [BulkInsert](https://entityframework-extensions.net/bulk-insert).

For set-based updates and deletes, see [ExecuteUpdate](/saving/execute-update) and [ExecuteDelete](/saving/execute-delete).

## Common Pitfalls

Be careful with the following mistakes.

### Forgetting SaveChangesAsync

`Add()` and `AddRange()` only start tracking entities.

This does not insert the row:

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
};

context.Products.Add(product);
```

You must call `SaveChangesAsync()` to persist the insert.

```csharp
await context.SaveChangesAsync();
```

### Expecting AddRange to Be BulkInsert

`AddRange()` is not a true bulk insert operation.

It adds multiple entities to the `ChangeTracker`, but `SaveChangesAsync()` still controls how the inserts are sent to the database.

For very large inserts, use [BulkInsert](https://entityframework-extensions.net/bulk-insert).

### Adding Related Entities Without Connecting the Graph

EF Core can insert related entities when they are part of the object graph being added.

If the related objects are not actually connected to the graph being added, EF Core may not insert the related data the way you expect.

A clear object graph is easier to understand and safer to save.

```csharp
var category = new Category
{
    Name = "Peripherals",
    Products = new List<Product>
    {
        new Product { Name = "Mechanical Keyboard", Price = 120 }
    }
};
```

### Using Add for an Existing Entity

`Add()` tells EF Core that the entity is new.

If you call `Add()` for an entity that already exists in the database, EF Core can try to insert it again.

For existing entities, use an update workflow instead.

For more information, see [Updating Data](/saving/modifying-data).

## External Resources - Adding Data

The following videos are useful if you want to see how EF Core inserts new entities, how the standard `Add()` / `AddRange()` + `SaveChanges()` workflow behaves, and when a real bulk insert library becomes more appropriate.

### Video 1 - Adding Data to a Database in EF Core 7

<iframe width="560" height="315" src="https://www.youtube.com/embed/6ONaI-3sJDc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video shows the basic insert workflow in EF Core 7: creating entity instances, adding them to the appropriate `DbSet` with `Add()`, and calling `SaveChanges()` to persist them in the database. It also demonstrates inserting a related entity by using an existing foreign key and verifies the generated primary key in the database. It is a useful visual introduction to the standard insert pattern before moving to `AddRange()`, related entity graphs, or bulk insert scenarios.

Key timestamps:

- [0:00](https://www.youtube.com/watch?v=6ONaI-3sJDc&t=0) — Introduction and entity setup using `Manager` and `Employee`
- [1:40](https://www.youtube.com/watch?v=6ONaI-3sJDc&t=100) — Adding an entity to the appropriate `DbSet` with `Add()`
- [2:39](https://www.youtube.com/watch?v=6ONaI-3sJDc&t=159) — Calling `SaveChanges()` and verifying the generated primary key in the database
- [3:36](https://www.youtube.com/watch?v=6ONaI-3sJDc&t=216) — Inserting a related `Employee` using an existing `Manager` foreign key
- [4:30](https://www.youtube.com/watch?v=6ONaI-3sJDc&t=270) — Recap of the three-step insert pattern

### Video 2 - Insert Data in ASP.NET Core with Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/cE_o8piz-2Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video shows how a basic `Add()` + `SaveChanges()` insert flow can be used inside an ASP.NET Core application. It demonstrates adding a new `Book` entity, saving it to SQL Server, retrieving the generated identity value after `SaveChanges()`, and debugging the insert flow through a repository and controller. It is useful if you want to see the insert pattern in a real web application. Since the video also spends time on repository, dependency injection, controller, and debugging code, it should be treated as a practical application example rather than a deep EF Core insert reference.

Key timestamps:

- [2:00](https://www.youtube.com/watch?v=cE_o8piz-2Y&t=120) — Creating the `AddNewBook` repository method and adding a new entity with `Add()`
- [4:00](https://www.youtube.com/watch?v=cE_o8piz-2Y&t=240) — Calling `SaveChanges()` and retrieving the generated identity value
- [7:00](https://www.youtube.com/watch?v=cE_o8piz-2Y&t=420) — Registering the repository with Dependency Injection
- [12:00](https://www.youtube.com/watch?v=cE_o8piz-2Y&t=720) — Debugging the POST action and verifying the insert in SQL Server

### Video 3 - EF Core Bulk Operations and BulkInsert Performance

<iframe width="560" height="315" src="https://www.youtube.com/embed/9QVPuuemvtE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video from ZZZ Projects demonstrates the performance difference between standard EF Core inserts using `AddRange()` + `SaveChanges()` and real bulk insert operations with Entity Framework Extensions. It compares insert performance, explains the cost of returning generated identity values, and shows how `IncludeGraph()` can be used for related entity graphs. It is especially useful when you want to understand why `AddRange()` is not the same as a true `BulkInsert`.

Key timestamps:

- [0:00](https://www.youtube.com/watch?v=9QVPuuemvtE&t=0) — Introduction to `SaveChanges()` and bulk insert benchmarks with 10,000 products
- [2:00](https://www.youtube.com/watch?v=9QVPuuemvtE&t=120) — Comparing `SaveChanges()` with `BulkInsert()` and generated identity values
- [4:00](https://www.youtube.com/watch?v=9QVPuuemvtE&t=240) — Using `BulkInsertOptimize()` and understanding optimized inserts
- [6:00](https://www.youtube.com/watch?v=9QVPuuemvtE&t=360) — Inserting related entity graphs with `IncludeGraph()`

## Summary

Adding data in EF Core uses the normal change-tracking and `SaveChangesAsync()` workflow.

Key points:

- use `Add()` to add one new entity
- use `AddRange()` to add multiple new entities
- call `SaveChangesAsync()` to send the `INSERT` statements to the database
- EF Core tracks new entities as `Added`
- related entities can be inserted together when they are part of the same object graph
- generated key values are available after `SaveChangesAsync()` succeeds
- `AddRange()` is not the same as a real `BulkInsert`

Use `Add()` and `AddRange()` for normal EF Core insert scenarios. For high-volume inserts, use [BulkInsert](https://entityframework-extensions.net/bulk-insert).

## Related Articles

If you want to understand how adding data fits into the broader EF Core saving workflow, these pages are the best next step:

- [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
- [Saving Data in Connected Scenario](/saving/save-changes-connected-entities) — how EF Core saves entities that are already tracked by the current context
- [ChangeTracker](/saving/change-tracker) — how EF Core stores entity states such as `Added`
- [Tracking Changes of Entities](/saving/change-tracker-how-it-works) — how EF Core detects changes before saving
- [Updating Data](/saving/modifying-data) — how to update existing entities with the normal tracked workflow
- [Deleting Data](/saving/deleting-data) — how to delete entities with the normal tracked workflow
- [Adding data via the DbContext](/dbcontext/adding-data) — another way to add entities using the `DbContext` API
- [Bulk Insert](https://entityframework-extensions.net/bulk-insert) — how to insert large datasets with Entity Framework Extensions

## FAQ

### Does Add insert the entity immediately?

No. `Add()` only starts tracking the entity as `Added`. The row is inserted when `SaveChangesAsync()` is called.

### Does AddRange insert rows immediately?

No. `AddRange()` starts tracking multiple entities as `Added`. The inserts are sent to the database when `SaveChangesAsync()` runs.

### What is the difference between Add and AddRange?

`Add()` is used for one entity. `AddRange()` is used for multiple entities.

Both methods use the normal EF Core `ChangeTracker` and `SaveChangesAsync()` workflow.

### Is AddRange the same as BulkInsert?

No. `AddRange()` is not a true bulk insert operation.

`AddRange()` tracks multiple entities, and `SaveChangesAsync()` sends the database commands. A real bulk insert library such as Entity Framework Extensions is designed for high-volume insert scenarios.

### Is AddRange faster than Add?

No, they provide similar performance. `AddRange` doesn’t magically save entities faster; it simply allows you to add a list to the change tracker instead of calling the `Add` method multiple times.

Even the EF Core team says there is almost no difference: “These methods are provided as a convenience. Using a ‘range’ method has the same functionality as multiple calls to the equivalent non-range method. There is no significant performance difference between the two approaches.”

Reference: [https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange)

### Can EF Core insert related entities automatically?

Yes. EF Core can insert related entities when they are reachable from the object graph being added.

For example, adding a `Category` with new `Product` entities in its `Products` collection can insert both the category and the products.

### How do I get the generated ID after inserting?

After `SaveChangesAsync()` succeeds, EF Core updates the entity instance with generated database values such as identity keys.

```csharp
context.Products.Add(product);

await context.SaveChangesAsync();

var id = product.Id;
```