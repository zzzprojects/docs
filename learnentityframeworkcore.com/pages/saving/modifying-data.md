---
title: Modifying data via the DbSet
description: An examination of the methods and approaches available for modifying data via the Entity Framework Core DbSet API 
canonical: /saving/modifying-data
status: Published
lastmod: 2026-06-01
---

# EF Core Updating Data

Updating data in EF Core usually means loading an existing entity, changing one or more property values, and calling [`SaveChangesAsync()`](/saving/save-changes).

It is useful when you want to modify existing rows using the normal EF Core [change-tracking workflow](/saving/change-tracker).

## Update a Tracked Entity

Most of the time, you will query the entity, change its values, and call `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

This updates the `Price` column for the matching product.

Because the product was loaded by the same `DbContext`, EF Core is already tracking it.

For more about this tracked workflow, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

### SQL Generated for a Tracked Update

Since EF Core knows which property changed, it usually updates only the modified column.

```sql
UPDATE [Products]
SET [Price] = @p0
WHERE [Id] = @p1;
```

The exact SQL can vary depending on the database provider.

## Update Multiple Properties

You can update more than one property on the same tracked entity before calling `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Name = "Ergonomic Wireless Mouse";
product.Price = 49;

await context.SaveChangesAsync();
```

This updates the changed properties in the database when `SaveChangesAsync()` runs.

## Update a Disconnected Entity

In disconnected scenarios, such as web applications or APIs, the entity is often not tracked by the current `DbContext`.

Use `Update()` when you want EF Core to treat an existing entity as modified.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Id = 10,
    Name = "Wireless Mouse",
    Price = 45
};

context.Products.Update(product);

await context.SaveChangesAsync();
```

This marks the entity as `Modified` and sends an `UPDATE` when `SaveChangesAsync()` runs.

Use this carefully because EF Core treats the entity as modified even if only one value changed.

## Update Multiple Disconnected Entities with UpdateRange

Use `UpdateRange()` when you want to mark multiple disconnected entities as modified in one call.

```csharp
using var context = new AppDbContext();

var keyboard = new Product
{
    Id = 1,
    Name = "Mechanical Keyboard",
    Price = 120
};

var mouse = new Product
{
    Id = 2,
    Name = "Wireless Mouse",
    Price = 45
};

var monitor = new Product
{
    Id = 3,
    Name = "27-inch Monitor",
    Price = 250
};

context.Products.UpdateRange(keyboard, mouse, monitor);

await context.SaveChangesAsync();
```

This marks all three products as modified.

`UpdateRange()` is useful for disconnected workflows, but it does not mean EF Core knows which properties actually changed.

## Update Specific Properties

When you only want to update one property on a disconnected entity, attach the entity and mark that property as modified.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Id = 10
};

context.Products.Attach(product);

product.Price = 45;

context.Entry(product)
    .Property(p => p.Price)
    .IsModified = true;

await context.SaveChangesAsync();
```

This tells EF Core to update only the `Price` property.

This pattern is useful when you want a partial update and do not want to send every property to the database.

## Update and UpdateRange Methods

EF Core provides methods for telling the `DbContext` that an entity should be updated.

The most common ones are:

- `Update<TEntity>(TEntity entity)`
- `UpdateRange<TEntity>(IEnumerable<TEntity> entities)`
- `UpdateRange<TEntity>(params TEntity[] entities)`

Use `Update()` when you want to mark one entity as modified.

```csharp
context.Products.Update(product);
```

Use `UpdateRange()` when you want to mark multiple entities as modified in one call.

```csharp
context.Products.UpdateRange(products);
```

These methods are mainly useful in disconnected scenarios, when EF Core is not already tracking the entity being saved.

For normal connected updates, you usually do not need `Update()`. Query the entity, modify its properties, and call `SaveChangesAsync()`.

## Important Behavior

Updating data in EF Core depends on whether the entity is already tracked by the current `DbContext`.

That means:

- a tracked entity can be updated by changing its property values
- `SaveChangesAsync()` sends the `UPDATE` statements to the database
- EF Core can detect which properties changed when the entity is tracked
- `Update()` marks an entity as `Modified`
- `UpdateRange()` marks multiple entities as `Modified`
- disconnected entities often need explicit tracking or state configuration
- partial updates can be done with `Attach()` and `Property(...).IsModified = true`

For a deeper explanation of the save pipeline, see [SaveChanges](/saving/save-changes).

For more about entity states, see [ChangeTracker](/saving/change-tracker).

For the official EF Core overview of adding, updating, and deleting data, see the Microsoft documentation on [basic SaveChanges usage](https://learn.microsoft.com/en-us/ef/core/saving/basic#updating-data).

## Connected vs Disconnected Updates

The most important distinction is whether the entity is already tracked.

In a connected scenario:

1. the same `DbContext` queries the entity
2. EF Core starts tracking it
3. you modify one or more properties
4. `SaveChangesAsync()` persists the detected changes

This is usually the clearest update workflow because EF Core already knows the original values and the current values.

In a disconnected scenario:

1. the entity was not loaded by the same `DbContext` that saves it
2. EF Core does not know which values changed before the entity was attached
3. you must tell EF Core how the entity should be tracked
4. methods such as `Update()`, `Attach()`, and `Property(...).IsModified = true` become important

For more about connected workflows, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

For more advanced disconnected graph scenarios, see [Entity Framework Core TrackGraph For Disconnected Data](http://www.mikesdotnetting.com/article/303/entity-framework-core-trackgraph-for-disconnected-data).

## Property-Level Updates

Sometimes you only want to update one property of a disconnected entity.

This is common in APIs, patch operations, or forms where only a few fields are allowed to change.

Instead of calling `Update()`, you can attach the entity and mark only the property you want to update as modified.

This pattern is useful when:

- you do not want to update every column
- you want to avoid overwriting values that were not sent by the client
- you want more control over disconnected updates
- you are implementing a partial update workflow

For broader tracking behavior, see [Tracking Changes of Entities](/saving/change-tracker-how-it-works).

## How Updating Data Works

When an entity is tracked, EF Core stores information about its state and property values.

In a connected update, the flow usually looks like this:

1. EF Core queries an entity from the database.
2. The entity is tracked as `Unchanged`.
3. You modify one or more properties.
4. EF Core detects the changed values.
5. `SaveChangesAsync()` generates the required `UPDATE`.
6. The database updates the matching row.
7. EF Core accepts the change and the entity becomes `Unchanged` again.

In a disconnected update, EF Core does not have the original tracked state.

That is why methods such as `Update()`, `Attach()`, and `Property(...).IsModified = true` are used to tell EF Core how the entity should be treated.

## Update vs ExecuteUpdate

`Update()` and `ExecuteUpdateAsync()` can both update data, but they are designed for different workflows.

`Update()` works with entity instances and the normal `ChangeTracker` + `SaveChangesAsync()` workflow.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Id = 10,
    Name = "Wireless Mouse",
    Price = 45
};

context.Products.Update(product);

await context.SaveChangesAsync();
```

`ExecuteUpdateAsync()` updates rows directly in the database from a query without loading or tracking entity instances.

```csharp
using var context = new AppDbContext();

await context.Products
    .Where(p => p.Price < 50)
    .ExecuteUpdateAsync(s => s
        .SetProperty(p => p.Price, 50));
```

Use `Update()` when you are working with entity instances.

Use [`ExecuteUpdateAsync()`](/saving/execute-update) when the update can be expressed as a set-based database rule.

## UpdateRange is Not BulkUpdate

`UpdateRange()` is useful when you want to mark multiple entities as modified in one call.

However, `UpdateRange()` is not the same as a real bulk update.

```csharp
using var context = new AppDbContext();

var products = new List<Product>
{
    new Product { Id = 1, Name = "Mechanical Keyboard", Price = 125 },
    new Product { Id = 2, Name = "Wireless Mouse", Price = 45 },
    new Product { Id = 3, Name = "27-inch Monitor", Price = 275 }
};

context.Products.UpdateRange(products);

await context.SaveChangesAsync();
```

This still uses the normal EF Core workflow:

1. entities are tracked
2. entities are marked as `Modified`
3. `SaveChangesAsync()` generates the database commands
4. EF Core sends the updates to the database

For small or moderate updates, `UpdateRange()` can be enough.

For thousands or millions of rows, a real bulk update library is usually a better choice.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

using var context = new AppDbContext();

var products = GetProductsToUpdate();

await context.BulkUpdateAsync(products);
```

[Entity Framework Extensions BulkUpdate](https://entityframework-extensions.net/bulk-update) is designed for high-volume update scenarios where performance matters more than the normal tracked entity workflow.

In short:

- use a tracked update when the entity is already loaded by the current context
- use `Update()` for one disconnected entity
- use `UpdateRange()` for multiple disconnected entities in the normal EF Core workflow
- use `BulkUpdate` when you need high-performance updates for large datasets

## When to Use Update or UpdateRange

Use `Update()` or `UpdateRange()` when:

- the entity is disconnected from the current `DbContext`
- you know the entity already exists in the database
- you want EF Core to treat the entity as modified
- you are saving data received from an API, form, or service layer
- you want to update one or more entities through the normal `SaveChangesAsync()` workflow

Common examples include:

- receiving a product from an API request
- saving an edited product from a form
- updating several disconnected products at once
- applying changes from a service layer where the original query context is no longer available

For connected workflows, prefer querying the entity first, modifying it, and calling `SaveChangesAsync()`.

## When Not to Use Update or UpdateRange

`Update()` and `UpdateRange()` are not always the best choice.

Avoid them when:

- the entity is already tracked by the current `DbContext`
- you only want to update one or two specific properties
- you do not want to mark the whole entity as modified
- the entity might be new instead of existing
- you need to update thousands or millions of rows efficiently
- the update can be expressed as a set-based database operation

For set-based updates that do not require loading entities, see [ExecuteUpdate](/saving/execute-update).

For high-volume updates from a list of entities, use [BulkUpdate](https://entityframework-extensions.net/bulk-update).

## Common Pitfalls

Be careful with the following mistakes.

### Calling Update on an Already Tracked Entity

If the entity was loaded by the same `DbContext`, you usually do not need `Update()`.

Changing the property and calling `SaveChangesAsync()` is enough.

Calling `Update()` on an already tracked entity can make the code less clear and may mark more properties as modified than necessary.

### Expecting Update to Know Which Properties Changed

In a disconnected scenario, EF Core does not know which properties changed before the entity was attached.

```csharp
using var context = new AppDbContext();

context.Products.Update(product);

await context.SaveChangesAsync();
```

This tells EF Core to treat the entity as modified.

If you only want to update specific properties, use `Attach()` and mark those properties explicitly.

### Updating an Entity Without a Key

`Update()` expects the entity to represent an existing row.

If the key value is missing or incorrect, EF Core may not be able to update the intended row.

```csharp
var product = new Product
{
    Name = "Wireless Mouse",
    Price = 45
};

context.Products.Update(product);

await context.SaveChangesAsync();
```

In this example, the key value is missing, so the update cannot reliably target an existing row.

### Expecting UpdateRange to Be BulkUpdate

`UpdateRange()` is not a true bulk update operation.

It marks multiple entities as modified, but `SaveChangesAsync()` still controls how the updates are sent to the database.

For very large updates, use [BulkUpdate](https://entityframework-extensions.net/bulk-update).

### Overwriting Values in Disconnected Updates

`Update()` can mark the whole entity as modified.

This can overwrite values that were not intended to change, especially when the entity was built from a partial API payload or form.

For partial updates, use a more explicit pattern such as `Attach()` with `Property(...).IsModified = true`.

This makes the update safer and easier to control.

## External Resources - Updating Data

The following videos are useful if you want to go deeper into how EF Core tracks updates, how `Update()` behaves in disconnected scenarios, how to control specific updated properties, and when `ExecuteUpdateAsync()` can be a better fit than the normal tracked workflow.

### Video 1 - How EF Core Keeps Track of Changes

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hubert Mijalski explains how EF Core uses the `ChangeTracker` to track entity states and decide what should be persisted when `SaveChanges()` runs. The video is especially useful for understanding the difference between a normal tracked update and explicitly calling `Update()` in disconnected scenarios.

It also shows why `Update()` can be risky when EF Core does not know which properties actually changed, since it may generate a broader update than expected.

**Key sections:**

- [00:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=0s) — Overview of ChangeTracker entity states
- [02:12](https://www.youtube.com/watch?v=uXDYEBexlYk&t=132s) — `AsNoTracking()` and why disconnected entities may require explicit tracking
- [05:40](https://www.youtube.com/watch?v=uXDYEBexlYk&t=340s) — Code demo comparing update with and without `Update()`
- [06:30](https://www.youtube.com/watch?v=uXDYEBexlYk&t=390s) — SQL logs showing partial update vs broader update behavior
- [07:30](https://www.youtube.com/watch?v=uXDYEBexlYk&t=450s) — Scoped ChangeTracker behavior per request

### Video 2 - Exclude a Property from Entity Update

<iframe width="560" height="315" src="https://www.youtube.com/embed/FuLuwpsR2Bw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This short video from dotnetexplained demonstrates a common issue when using `Update()`: properties that should not change can still be included in the generated update. It shows how to use `Entry(...).Property(...).IsModified = false` to exclude a property from the update.

This is useful for understanding property-level update control in disconnected scenarios. The article uses the opposite pattern, `IsModified = true`, to update only one specific property, but both approaches are based on the same idea: explicitly controlling which columns EF Core should update.

**Key sections:**

- [00:00](https://www.youtube.com/watch?v=FuLuwpsR2Bw&t=0s) — Introduction to the problem: `Update()` can include unwanted properties
- [00:45](https://www.youtube.com/watch?v=FuLuwpsR2Bw&t=45s) — Demo showing an unwanted property being included in the update
- [01:30](https://www.youtube.com/watch?v=FuLuwpsR2Bw&t=90s) — Using `IsModified = false` to exclude a property
- [02:30](https://www.youtube.com/watch?v=FuLuwpsR2Bw&t=150s) — Verifying the generated SQL after controlling modified properties

### Video 3 - EF Core 7 Changed How You Update Data

<iframe width="560" height="315" src="https://www.youtube.com/embed/3gRpG6s1MYU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Rahul Pandey compares the traditional EF Core update workflow with `ExecuteUpdateAsync()` in EF Core 7+. The video shows the usual pattern of loading an entity, modifying it, and calling `SaveChanges()`, then demonstrates how `ExecuteUpdateAsync()` can update rows directly in the database without loading or tracking entity instances.

This resource is useful if you want to understand when a set-based update can be more efficient than a normal tracked update, and why `ExecuteUpdateAsync()` bypasses the `ChangeTracker` and `SaveChanges()` pipeline.

**Key sections:**

- [00:00](https://www.youtube.com/watch?v=3gRpG6s1MYU&t=0s) — Why the traditional update pattern can require multiple database operations
- [02:00](https://www.youtube.com/watch?v=3gRpG6s1MYU&t=120s) — Traditional update workflow: query, modify, and call `SaveChanges()`
- [04:00](https://www.youtube.com/watch?v=3gRpG6s1MYU&t=240s) — Using `ExecuteUpdateAsync()` with `Where()` and `SetProperty()`
- [06:00](https://www.youtube.com/watch?v=3gRpG6s1MYU&t=360s) — Updating multiple properties and reviewing the generated SQL
- [08:00](https://www.youtube.com/watch?v=3gRpG6s1MYU&t=480s) — Important caveat: `ExecuteUpdateAsync()` does not use `ChangeTracker` or `SaveChanges()`

## Summary

Updating data in EF Core depends on whether the entity is already tracked by the current `DbContext`.

Key points:

- in a connected scenario, query the entity, change its properties, and call `SaveChangesAsync()`
- EF Core can detect changed properties when the entity is tracked
- `Update()` marks one disconnected entity as `Modified`
- `UpdateRange()` marks multiple disconnected entities as `Modified`
- `Attach()` with `Property(...).IsModified = true` is useful for partial updates
- `UpdateRange()` is not the same as a real `BulkUpdate`
- for set-based updates, use `ExecuteUpdateAsync()`
- for high-volume entity-list updates, see [BulkUpdate](https://entityframework-extensions.net/bulk-update)

Use tracked updates when the entity is already loaded by the current context.

Use `Update()` or `UpdateRange()` for disconnected entities when you want EF Core to treat them as modified.

## Related Articles

If you want to understand how updating data fits into the broader EF Core saving workflow, these pages are the best next steps:

- [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
- [Saving Data in Connected Scenario](/saving/save-changes-connected-entities) — how EF Core saves entities already tracked by the current context
- [ChangeTracker](/saving/change-tracker) — how EF Core stores entity states such as `Modified`
- [Tracking Changes of Entities](/saving/change-tracker-how-it-works) — how EF Core detects changes before saving
- [ExecuteUpdate](/saving/execute-update) — how to update rows directly in the database without loading or tracking entities
- [Bulk Update](https://entityframework-extensions.net/bulk-update) — how to update large datasets with Entity Framework Extensions

## FAQ

### How do I update an entity in EF Core?

In a connected scenario, query the entity, change its properties, and call `SaveChangesAsync()`.

If the entity was loaded by the same `DbContext`, EF Core is already tracking it and can detect the changed values.

### Do I need to call Update after querying an entity?

Usually no. If the entity was loaded by the same `DbContext`, EF Core is already tracking it.

Changing the property and calling `SaveChangesAsync()` is enough.

### What does Update do in EF Core?

`Update()` tells EF Core to track an entity as `Modified`.

It is mainly useful when the entity is disconnected from the current `DbContext`.

### What is the difference between Update and UpdateRange?

`Update()` marks one entity as modified.

`UpdateRange()` marks multiple entities as modified in one call.

Both methods use the normal EF Core `ChangeTracker` and `SaveChangesAsync()` workflow.

### Does Update only update changed properties?

Not always.

For tracked entities, EF Core can detect which properties changed.

For disconnected entities passed to `Update()`, EF Core usually treats the entity as modified because it does not know which values changed before the entity was attached.

### How do I update only one property?

Attach the entity and mark only the property you want to update as modified using `Property(...).IsModified = true`.

This pattern is useful for partial updates, especially when you do not want to mark the whole entity as modified.

### Is UpdateRange the same as BulkUpdate?

No. `UpdateRange()` is not a true bulk update operation.

`UpdateRange()` marks multiple entities as modified, and `SaveChangesAsync()` sends the database commands. A real bulk update library such as Entity Framework Extensions is designed for high-volume update scenarios.

### When should I use ExecuteUpdate instead?

Use `ExecuteUpdateAsync()` when the update can be expressed as a database-side rule and you do not need to load or track entities.

Use `Update()` or `UpdateRange()` when you are working with entity instances in the normal EF Core saving workflow.