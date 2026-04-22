---
title: EF Core SaveChanges - How Data Is Persisted (Tracking, Transactions, and Pitfalls)
description: Learn what SaveChanges does in Entity Framework Core, how it uses the ChangeTracker, what happens under the hood, transaction behavior, common scenarios, performance tips, and FAQs.
canonical: /saving/save-changes
status: Published
lastmod: 2026-04-22
---

# SaveChanges in EF Core: How to Persist Changes

`SaveChanges()` is the EF Core method used to persist tracked changes from the current `DbContext` to the database. In modern applications, `SaveChangesAsync()` is usually the preferred variant.

Those changes can include inserts, updates, and deletes. If you want to go deeper into each operation, see [Adding Data](/saving/adding-data), [Updating Data](/saving/modifying-data), and [Deleting Data](/saving/deleting-data).

If tracking is misunderstood, saving can feel unpredictable because EF Core only persists changes for entities tracked by the current context.

As shown later in this page, multiple operations can also be [combined in a single save](#mix-multiple-operations).

## SaveChanges

In the standard EF Core workflow, you create new entities or modify tracked entities, and then call `SaveChangesAsync()` to persist those changes to the database.

```csharp
using var context = new AppDbContext();

context.Products.Add(new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
});

await context.SaveChangesAsync();
```

## SaveChangesAsync(bool acceptAllChangesOnSuccess)

`SaveChangesAsync()` also provides an overload that lets you control whether EF Core should automatically accept tracked changes after a successful save.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync(acceptAllChangesOnSuccess: false);
```

## Rows affected

`SaveChangesAsync()` returns an `int` value that indicates how many tracked entries were written during the save operation.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

var rowsAffected = await context.SaveChangesAsync(); // return 1
```

In this example, `rowsAffected` contains the value `1` returned by the save operation.

This is often useful for basic validation, diagnostics, or logging, but it should not be interpreted as a full replacement for database-level execution details.

## Common Save Scenarios

The following examples show the most common ways `SaveChangesAsync()` is used in EF Core.

### Insert a new entity

To insert a new entity, create the object, add it to the context, and call `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .SingleAsync(c => c.Name == "Peripherals");

var product = new Product
{
    Name = "Mechanical Keyboard",
    Price = 120,
    CategoryId = category.Id
};

context.Products.Add(product);

await context.SaveChangesAsync();
```

For more insertion patterns, including `AddRange` and related entities, see [Adding Data](/saving/adding-data). If you want to review how `AddRange` and similar methods behave, see the EF Core documentation on [`AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange).

### Update an existing entity

To update an existing entity, query it first so EF Core starts tracking it, modify one or more properties, and then call `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

Once the entity is tracked, EF Core detects the property change and persists it during the save operation.

For more update patterns, including disconnected updates and partial updates, see [Updating Data](/saving/modifying-data).

### Delete an entity

To delete an entity, query it, mark it for removal with the `Remove` or `RemoveRange` method, and then save the change.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "27-inch Monitor");

context.Products.Remove(product);

await context.SaveChangesAsync();
```

For more delete patterns, including `RemoveRange`, see [Deleting Data](/saving/deleting-data).

### Mix multiple operations

One of the most useful aspects of `SaveChangesAsync()` is that it can persist different kinds of tracked changes together.

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .SingleAsync(c => c.Name == "Peripherals");

var mouse = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

var monitor = await context.Products
    .SingleAsync(p => p.Name == "27-inch Monitor");

context.Products.Add(new Product
{
    Name = "Mechanical Keyboard",
    Price = 120,
    CategoryId = category.Id
});

mouse.Price = 45;
context.Products.Remove(monitor);

await context.SaveChangesAsync();
```

In a single call, EF Core inserts a new product, updates another one, and deletes a third one.

## How SaveChangesAsync Works

At a high level, `SaveChangesAsync()` reads the changes currently tracked by the `DbContext`, converts them into database commands, sends those commands to the database, and updates entity states after a successful save.

### Detects tracked changes

EF Core uses the current `DbContext` to track entity instances and their states through the [ChangeTracker](/saving/change-tracker). Before saving, it determines which tracked entities were added, modified, or marked for deletion.

That is why the examples in this article follow the same basic pattern:

* add a new entity
* modify a tracked entity
* remove a tracked entity
* call `SaveChangesAsync()`

If nothing is tracked as changed, `SaveChangesAsync()` does not persist anything.

For a deeper explanation of how EF Core tracking works internally, see [Change Tracker: How It Works](/saving/change-tracker-how-it-works).

### Builds the required commands

Once EF Core knows which tracked entities changed, it generates the corresponding database commands needed to persist those changes.

Depending on the tracked state, that usually means generating commands for:

* inserts
* updates
* deletes

The exact SQL sent to the database can vary depending on the provider and the operations being performed. If you want to inspect the generated SQL during development, see the [official EF Core simple logging documentation](https://learn.microsoft.com/en-us/ef/core/logging-events-diagnostics/simple-logging). For a broader overview of saving patterns in EF Core, see the [official saving data overview](https://learn.microsoft.com/en-us/ef/core/saving/).

### Sends changes when save is called

Tracking a change does not immediately write anything to the database.

For example, calling `Add`, changing a property, or calling `Remove` only updates what EF Core knows about the entity inside the current context. The actual persistence happens when `SaveChangesAsync()` runs.

This separation is important because it allows multiple changes to be collected and persisted together in one call.

### Updates entity states after success

If the save succeeds, EF Core normally accepts the tracked changes automatically. That is the default behavior of `SaveChangesAsync()`.

Inserted or updated tracked entities typically move to an `Unchanged` state after a successful save. Deleted entities are typically detached.

When you call `SaveChangesAsync(false)`, EF Core still sends the changes to the database, but it does not automatically perform that acceptance step afterward.

### Returns the number of written entries

After the save operation completes, `SaveChangesAsync()` returns the number of tracked entries written during that call.

That return value is often useful for basic validation, diagnostics, or logging, but it should not be treated as a complete replacement for database-level execution details.

### Transaction behavior

By default, EF Core uses a transaction during `SaveChanges()` to avoid partial updates.

That means:

* if all commands succeed, the transaction is committed
* if a command fails, the save fails and an exception is thrown

For more advanced transaction behavior, see the [official EF Core transactions documentation](https://learn.microsoft.com/en-us/ef/core/saving/transactions).

## When to Use It

`SaveChangesAsync()` is the right choice when you are working with entities tracked by the current `DbContext` and want EF Core to persist those changes through its standard save pipeline.

This is the most common approach when you:

* create new entities and want to insert them
* modify tracked entities and want EF Core to generate updates
* remove tracked entities and want EF Core to delete them
* combine multiple tracked changes in a single unit of work

It is also a good fit when you want your application code to stay close to the domain model rather than expressing every operation as a direct database command.

For tracked update workflows specifically, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

## When Not to Use It

`SaveChangesAsync()` is not the best fit for every persistence scenario.

You may want a different approach when:

* you need to update rows directly in the database without loading entities first
* you want to delete matching rows without tracking them in the current context
* the operation is primarily set-based rather than entity-based
* the volume is high enough that the standard tracked save pipeline becomes a bottleneck

In those cases, APIs such as [ExecuteUpdateAsync](/saving/execute-update), [ExecuteDeleteAsync](/saving/execute-delete), raw SQL, or specialized bulk operations may be more appropriate. For very large save workloads, see also [BulkSaveChanges / Entity Framework Extensions](https://entityframework-extensions.net/bulk-savechanges).

The key distinction is simple: `SaveChangesAsync()` is designed for persisting **tracked entity changes**, not for every possible database write pattern.

## Brief Comparison

### SaveChangesAsync vs ExecuteUpdateAsync

`SaveChangesAsync()` works with tracked entities in the current `DbContext`. You modify objects in memory, and EF Core persists those changes when the save operation runs.

`ExecuteUpdateAsync()`, by contrast, updates matching rows directly in the database without loading them into the context first. That makes it more suitable for set-based updates where tracking is unnecessary.

Use `SaveChangesAsync()` when your workflow is centered on tracked entities. Use `ExecuteUpdateAsync()` when you want to apply a database-side update directly to a query result.

### SaveChangesAsync vs ExecuteDeleteAsync

`SaveChangesAsync()` deletes entities that are being tracked and marked for removal.

`ExecuteDeleteAsync()` deletes matching rows directly in the database without requiring those entities to be loaded and tracked first.

Use `SaveChangesAsync()` when the delete is part of a normal tracked entity workflow. Use `ExecuteDeleteAsync()` when you want a direct database-side delete based on a query.

## External Resources

The following resources are useful if you want to go deeper into how EF Core persists changes, how the `ChangeTracker` influences saving behavior, and why some `SaveChanges()` patterns scale better than others.

They are especially helpful for understanding entity states, tracking-related pitfalls, batching behavior, and when standard `SaveChanges()` may no longer be the best fit for high-volume operations.

### Video 1 — How does EF Core keep track of changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk?si=ecJMtk1ZxKjv4JOs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hubert Mijalski explains how EF Core uses the `ChangeTracker` internally, with a focus on entity states and what `SaveChanges()` actually persists.

This video is especially useful if you want to understand why saving can lead to unexpected updates, particularly in disconnected scenarios or when `Update()` is used too broadly.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=9s) — ChangeTracker overview and entity states (`Added`, `Unchanged`, `Deleted`, `Detached`)
* [05:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=300s) — `AsNoTracking()` vs `Update()` and why `Update()` can be risky or overused
* [09:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=540s) — ChangeTracker scope in web applications and the common “one SaveChanges per unit of work” pattern
* [12:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=720s) — Code demo showing a normal tracked update versus `Update()` with SQL logs

### Video 2 — How SaveChanges works and 1 common mistake (EF Core / .NET 8)

<iframe width="560" height="315" src="https://www.youtube.com/embed/lehYoEhFiIM?si=clN4t9FuuBZshX5i" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Pawel Minkina explains how `SaveChanges()` works using a simple analogy, then connects that explanation to batching benchmarks and a common performance mistake.

This is a useful companion resource if you want to understand why calling `SaveChanges()` once per entity is often inefficient, and why change detection can become expensive in large save operations.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=lehYoEhFiIM&t=1s) — Introduction and analogy for how `DbContext` and `SaveChanges()` work together
* [02:30](https://www.youtube.com/watch?v=lehYoEhFiIM&t=150s) — Benchmark comparing batched saving versus calling `SaveChanges()` repeatedly
* [05:00](https://www.youtube.com/watch?v=lehYoEhFiIM&t=300s) — Large-scale run (5 million items) and recreating the `DbContext` periodically to reduce memory pressure
* [07:30](https://www.youtube.com/watch?v=lehYoEhFiIM&t=450s) — `AutoDetectChangesEnabled = false` and why it can improve performance for large save operations

### Video 3 — Boost EF Core Performance: BulkInsert, BulkUpdate, BulkDelete & More (Full Guide)

<iframe width="560" height="315" src="https://www.youtube.com/embed/VG-aU7WRl5g?si=M4AVM6duUS9hs8jh" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Anton Martyniuk (ZZZ Projects) walks through why standard `SaveChanges()` can become slower at scale, then demonstrates bulk-oriented alternatives and when they start to make a measurable difference.

This is a useful follow-up resource for scenarios where the standard tracked save pipeline is no longer enough for the required volume, and you need to evaluate more specialized persistence strategies.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=VG-aU7WRl5g&t=91s) — Introduction and benchmark: `SaveChanges()` with 10k records
* [03:00](https://www.youtube.com/watch?v=VG-aU7WRl5g&t=180s) — `BulkInsert` vs `BulkInsertOptimized`
* [08:00](https://www.youtube.com/watch?v=VG-aU7WRl5g&t=480s) — `BulkUpdate` and `BulkDelete`, including options such as key matching and `IncludeGraph`
* [12:00](https://www.youtube.com/watch?v=VG-aU7WRl5g&t=720s) — `BulkMerge` and `BulkSynchronize` for combined insert/update/delete workflows

If you want additional reading on large insert workloads and range methods, see [4 Best Ways to Do Bulk Inserts in Entity Framework](https://www.entityframeworktutorial.net/efcore/4-best-ways-to-do-bulk-inserts-in-entity-framework.aspx) and the EF Core documentation on [`AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange).

## Summary

`SaveChanges()` is the standard EF Core method for persisting tracked entity changes.

In practice, the most important ideas are:

* tracked changes are only persisted when `SaveChangesAsync()` runs
* inserts, updates, and deletes can be combined in a single save operation
* `SaveChangesAsync(bool acceptAllChangesOnSuccess)` gives you more control in advanced scenarios
* the method works best for tracked entity workflows, not every database write pattern

If you need more depth, the related pages below cover the most important save scenarios in more detail.

## Related Articles

If you want to explore the most closely related save scenarios, the following pages are a good next step:

* [Adding Data](/saving/adding-data) — how to insert new entities, including `Add`, `AddRange`, and related entities
* [Updating Data](/saving/modifying-data) — how to update existing entities in tracked and disconnected scenarios
* [Deleting Data](/saving/deleting-data) — how to remove entities with `Remove` and `RemoveRange`
* [Connected Entities](/saving/save-changes-connected-entities) — how EF Core behaves when entities are already tracked by the current context
* [Change Tracker](/saving/change-tracker) — how EF Core tracks entity states and detects changes

## FAQ

### What does SaveChanges return in EF Core?

It returns the number of tracked entries written during the save operation.

### Does SaveChanges always run inside a transaction?

By default, EF Core uses a transaction during save operations to avoid partial updates.

### Why do I get `DbUpdateException`?

Usually because the database rejected one of the generated commands, for example due to a constraint violation, invalid data, or another database-side error.

### Why do I get `DbUpdateConcurrencyException`?

Usually because an expected update or delete affected no rows, often because the data changed in the database after it was originally loaded.