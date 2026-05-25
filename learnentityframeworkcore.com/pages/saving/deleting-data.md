---
title: Deleting data via the DbSet
description: An examination of the methods and approaches available for deleting data via the Entity Framework Core DbSet API 
canonical: /saving/deleting-data
status: Published
lastmod: 2026-05-25
---

# EF Core Deleting Data

`Remove()` and `RemoveRange()` mark entities as `Deleted` so they can be removed from the database when [`SaveChangesAsync()`](/saving/save-changes) is called.

It is useful when you want to delete one or more rows using the normal EF Core [change-tracking workflow](/saving/change-tracker).

## Delete a Tracked Entity

Most of the time, you will query the entity, remove it, and call `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

context.Products.Remove(product);

await context.SaveChangesAsync();
```

This deletes the matching product from the database.

### SQL Generated for a Tracked Delete

When `SaveChangesAsync()` runs, EF Core sends a `DELETE` statement for the entity marked as `Deleted`.

```sql
DELETE FROM [Products]
WHERE [Id] = @p0;
```

The exact SQL can vary depending on the database provider.

## Delete a Disconnected Entity

If you already know the key of the entity you want to delete, you can create an entity instance with only the key value and pass it to `Remove()`.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Id = 10
};

context.Products.Remove(product);

await context.SaveChangesAsync();
```

This deletes the product with the matching key.

## Delete Multiple Entities with RemoveRange

Use `RemoveRange()` when you want to delete multiple entities in one call.

```csharp
using var context = new AppDbContext();

var keyboard = new Product { Id = 1 };
var mouse = new Product { Id = 2 };
var monitor = new Product { Id = 3 };

context.Products.RemoveRange(keyboard, mouse, monitor);

await context.SaveChangesAsync();
```

This deletes the three products from the database.

## Remove and RemoveRange Methods

EF Core provides methods for telling the `DbContext` that an entity should be deleted.

The most common ones are:

- `Remove<TEntity>(TEntity entity)`
- `RemoveRange<TEntity>(IEnumerable<TEntity> entities)`
- `RemoveRange<TEntity>(params TEntity[] entities)`

Use `Remove()` when you want to delete one entity.

```csharp
context.Products.Remove(product);
```

Use `RemoveRange()` when you want to mark multiple entities as deleted in one call.

```csharp
context.Products.RemoveRange(products);
```

Both methods use the normal EF Core change-tracking workflow. The entities are tracked first, and the database deletes are sent when `SaveChangesAsync()` runs.

For more details about range methods such as `AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`, see the official EF Core documentation on [range methods](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange).

## Important Behavior

Deleting data with EF Core follows the normal `ChangeTracker` and `SaveChangesAsync()` workflow.

That means:

- `Remove()` does not delete the row immediately
- `RemoveRange()` does not delete rows immediately
- entities are tracked as `Deleted`
- `SaveChangesAsync()` sends the `DELETE` statements to the database
- a tracked entity can be deleted after it is queried by the current `DbContext`
- a disconnected entity can be deleted if you provide a valid key value
- related data behavior depends on how the relationship is configured
- the database can reject the delete if foreign key constraints are not satisfied

For a deeper explanation of how tracked changes are persisted, see [SaveChanges](/saving/save-changes).

For more about how EF Core stores entity states, see [ChangeTracker](/saving/change-tracker).

For the official EF Core overview of adding, updating, and deleting data, see the Microsoft documentation on [basic SaveChanges usage](https://learn.microsoft.com/en-us/ef/core/saving/basic#deleting-data).

## How Deleting Data Works

When you remove an entity, EF Core tracks it in the `Deleted` state.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

context.Products.Remove(product);
```

At this point, the row has not been deleted from the database yet. The delete operation is sent only when `SaveChangesAsync()` runs.

A simplified flow looks like this:

1. You query or create an entity instance with a valid key.
2. You call `Remove()` or `RemoveRange()`.
3. EF Core tracks the entity as `Deleted`.
4. You call `SaveChangesAsync()`.
5. EF Core sends the `DELETE` statement to the database.
6. The database deletes the matching row.
7. EF Core stops tracking the deleted entity after the save succeeds.

For a deeper look at how EF Core tracks entity states, see [Tracking Changes of Entities](/saving/change-tracker-how-it-works).

## Deleting Related Data

Deleting an entity can affect related data.

For example, if a `Category` has related `Product` entities, deleting the category may also affect the products depending on how the relationship is configured.

A [fully defined relationship](/configuration/one-to-many-relationship-configuration#fully-defined-relationship) can use different delete behaviors, such as deleting related rows or setting foreign key values to `null`. A relationship configured with the Fluent API can also define [cascading referential integrity constraints](/configuration/one-to-many-relationship-configuration#cascading-referential-integrity-constraints).

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .Include(c => c.Products)
    .SingleAsync(c => c.Name == "Peripherals");

context.Categories.Remove(category);

await context.SaveChangesAsync();
```

This removes the category. What happens to the related products depends on the configured delete behavior and the database constraints.

Depending on the relationship, related rows may be deleted, foreign key values may be set to `null`, or the database may reject the delete.

For more about relationship delete behavior, see [referential constraint action options](/relationships/referential-constraint-action-options).

For more about shadow foreign key properties, see [Shadow Properties](/model/shadow-properties).

This article focuses on deleting entities with `Remove()` and `RemoveRange()`. Relationship delete behavior is usually covered separately because it depends on cascade rules, foreign keys, and database constraints.

## RemoveRange is Not BulkDelete

`RemoveRange()` is useful when you want to mark multiple entities as deleted in one call.

However, `RemoveRange()` is not the same as a real bulk delete.

```csharp
using var context = new AppDbContext();

var products = new List<Product>
{
    new Product { Id = 1 },
    new Product { Id = 2 },
    new Product { Id = 3 }
};

context.Products.RemoveRange(products);

await context.SaveChangesAsync();
```

This still uses the normal EF Core workflow:

1. entities are tracked
2. entities are marked as `Deleted`
3. `SaveChangesAsync()` generates the database commands
4. EF Core sends the deletes to the database

For small or moderate deletes, `RemoveRange()` can be enough.

For thousands or millions of rows, a real bulk delete library is usually a better choice.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

using var context = new AppDbContext();

var products = GetProductsToDelete();

await context.BulkDeleteAsync(products);
```

[Entity Framework Extensions BulkDelete](https://entityframework-extensions.net/bulk-delete) is designed for high-volume delete scenarios where performance matters more than the normal tracked entity workflow.

In short:

- use a tracked delete when the entity is already loaded by the current context
- use `Remove()` for one disconnected entity
- use `RemoveRange()` for multiple entities in the normal EF Core workflow
- use `BulkDelete` when you need high-performance deletes for large datasets
- use [`ExecuteDeleteAsync()`](/saving/execute-delete) when the delete can be expressed as a set-based database rule

## When to Use Remove or RemoveRange

Use `Remove()` or `RemoveRange()` when:

- you want to delete entities using the normal EF Core workflow
- you need EF Core to track the entities before saving
- you want to delete one entity or a moderate number of entities
- you already have entity instances to delete
- you want to combine deletes with inserts or updates in the same `SaveChangesAsync()` call
- you are working inside a tracked unit of work

Common examples include:

- deleting a product after loading it from the database
- deleting a product by key in a disconnected workflow
- deleting several selected products from a form or API request
- combining deletes with other tracked changes in the same save operation

For a broader overview of saving tracked changes, see [SaveChanges](/saving/save-changes).

## When Not to Use Remove or RemoveRange

`Remove()` and `RemoveRange()` are not always the best choice.

Avoid them when:

- you need to delete thousands or millions of rows as fast as possible
- the delete can be expressed as a database-side rule
- you do not need the normal `ChangeTracker` workflow
- you only need to delete rows matching a query filter
- loading or creating entity instances adds unnecessary overhead
- the main challenge is deciding what should happen to related rows

For set-based deletes that do not require loading entities, see [ExecuteDelete](/saving/execute-delete).

For high-volume deletes from a list of entities, use [BulkDelete](https://entityframework-extensions.net/bulk-delete).

## Common Pitfalls

Be careful with the following mistakes.

### Forgetting SaveChangesAsync

`Remove()` and `RemoveRange()` only mark entities as `Deleted`.

This does not delete the row immediately:

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Id = 10
};

context.Products.Remove(product);
```

You must call `SaveChangesAsync()` to persist the delete.

```csharp
await context.SaveChangesAsync();
```

### Deleting an Entity Without a Key

When you delete a disconnected entity, EF Core needs a valid key to know which row should be deleted.

```csharp
var product = new Product();

context.Products.Remove(product);

await context.SaveChangesAsync();
```

In this example, the key value is missing, so EF Core cannot reliably target the intended row.

A safer disconnected delete provides the key value.

```csharp
var product = new Product
{
    Id = 10
};

context.Products.Remove(product);

await context.SaveChangesAsync();
```

### Expecting RemoveRange to Be BulkDelete

`RemoveRange()` is not a true bulk delete operation.

It marks multiple entities as deleted, but `SaveChangesAsync()` still controls how the deletes are sent to the database.

For very large deletes, use [BulkDelete](https://entityframework-extensions.net/bulk-delete) or [`ExecuteDeleteAsync()`](/saving/execute-delete), depending on the scenario.

### Ignoring Related Data

Deleting a principal entity can affect related entities.

Depending on the relationship configuration, related rows may be deleted, updated, or may block the delete operation.

Before deleting entities with relationships, make sure you understand the configured delete behavior.

### Loading Entities Only to Delete by Filter

If you only need to delete rows that match a condition, loading all entities first may be unnecessary.

For example, this uses the normal tracked workflow:

```csharp
using var context = new AppDbContext();

var products = await context.Products
    .Where(p => p.Price < 10)
    .ToListAsync();

context.Products.RemoveRange(products);

await context.SaveChangesAsync();
```

This can be useful when you need entity instances or tracking behavior.

But if the operation is a simple set-based delete, [`ExecuteDeleteAsync()`](/saving/execute-delete) may be more appropriate.

```csharp
using var context = new AppDbContext();

await context.Products
    .Where(p => p.Price < 10)
    .ExecuteDeleteAsync();
```

## External Resources - Deleting Data

The following videos are useful if you want to understand the normal EF Core delete workflow, how entities are marked as `Deleted`, how disconnected deletes work, and when `ExecuteDeleteAsync()` may be a better option.

### Video 1 - Do You Know The Fastest Way To Delete Data With EF Core?

<iframe width="560" height="315" src="https://www.youtube.com/embed/hSSatvEOp3w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović compares different ways to delete data in EF Core, starting with the standard tracked workflow: load the entity, call `Remove()`, and then call `SaveChanges()`. The article uses `SaveChangesAsync()`, but the same tracked workflow applies.

He then shows how a disconnected delete can avoid loading the full entity by attaching an entity with a key and marking it as deleted.

The video is especially useful for understanding the trade-off between the normal `ChangeTracker` workflow and more direct database-side deletes with `ExecuteDeleteAsync()` in EF Core 7+.

**Key sections:**

- [00:00](https://www.youtube.com/watch?v=hSSatvEOp3w&t=0s) — Introduction and setup for deleting a `Product` entity
- [01:30](https://www.youtube.com/watch?v=hSSatvEOp3w&t=90s) — Standard tracked delete using `FindAsync()`, `Remove()`, and `SaveChanges()`
- [04:00](https://www.youtube.com/watch?v=hSSatvEOp3w&t=240s) — Disconnected delete using `Attach()` and `EntityState.Deleted`
- [07:12](https://www.youtube.com/watch?v=hSSatvEOp3w&t=432s) — Important caveat when the row does not exist
- [08:32](https://www.youtube.com/watch?v=hSSatvEOp3w&t=512s) — Using `ExecuteDeleteAsync()` in EF Core 7+ without loading entities

### Video 2 - How Does EF Core Keep Track of Changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hubert Mijalski explains how EF Core uses the `ChangeTracker` to track entity states before `SaveChanges()` runs. Although the video covers change tracking more broadly, it includes a useful delete scenario showing how an entity moves through the tracked workflow when `Remove()` is called.

This resource is especially helpful if you want to understand why `Remove()` does not delete the row immediately, and how EF Core marks an entity as `Deleted` before sending the final database command.

**Key sections:**

- [00:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=0s) — Overview of what the `ChangeTracker` does
- [01:18](https://www.youtube.com/watch?v=uXDYEBexlYk&t=78s) — Entity states in a DELETE scenario
- [02:05](https://www.youtube.com/watch?v=uXDYEBexlYk&t=125s) — `AsNoTracking()` and why tracking matters
- [04:46](https://www.youtube.com/watch?v=uXDYEBexlYk&t=286s) — `DbContext` lifecycle and tracked entities

### Video 3 - How to Delete Records in a Database with Entity Framework Core with the Attach Method

<iframe width="560" height="315" src="https://www.youtube.com/embed/8TnyFlD8ZUU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This short video from Coding Under Pressure demonstrates another disconnected delete pattern in EF Core using `Attach()`, `Remove()`, and `SaveChanges()`. It shows how an entity with only a primary key value can be attached to the context and then marked for deletion without first querying the full row from the database.

It is useful as a focused complement to the main delete examples, especially when you want to understand how EF Core can delete an entity that was not originally loaded by the current `DbContext`.

**Key sections:**

- [00:00](https://www.youtube.com/watch?v=8TnyFlD8ZUU&t=0s) — Introduction and context for the EF Core delete example
- [00:45](https://www.youtube.com/watch?v=8TnyFlD8ZUU&t=45s) — Explaining `Attach()` as an alternative to querying the entity first
- [01:30](https://www.youtube.com/watch?v=8TnyFlD8ZUU&t=90s) — Creating an entity with a key, attaching it, and calling `Remove()`
- [02:30](https://www.youtube.com/watch?v=8TnyFlD8ZUU&t=150s) — Running the code and confirming the delete after `SaveChanges()`

## Summary

Deleting data in EF Core uses the normal change-tracking and `SaveChangesAsync()` workflow.

Key points:

- use `Remove()` to delete one entity
- use `RemoveRange()` to delete multiple entities
- call `SaveChangesAsync()` to send the `DELETE` statements to the database
- EF Core tracks removed entities as `Deleted`
- a tracked entity can be deleted after it is queried by the current `DbContext`
- a disconnected entity can be deleted when you provide a valid key value
- related data behavior depends on relationship configuration and database constraints
- `RemoveRange()` is not the same as a real `BulkDelete`

Use `Remove()` and `RemoveRange()` for normal EF Core delete scenarios. For set-based deletes, use [`ExecuteDeleteAsync()`](/saving/execute-delete). For high-volume deletes from a list of entities, use [BulkDelete](https://entityframework-extensions.net/bulk-delete).

## Related Articles

If you want to understand how deleting data fits into the broader EF Core saving workflow, these pages are the best next steps:

- [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
- [Saving Data in Connected Scenario](/saving/save-changes-connected-entities) — how EF Core saves entities that are already tracked by the current context
- [ChangeTracker](/saving/change-tracker) — how EF Core stores entity states such as `Deleted`
- [Tracking Changes of Entities](/saving/change-tracker-how-it-works) — how EF Core detects and tracks entity state changes
- [Updating Data](/saving/modifying-data) — how to update existing entities with a similar tracked workflow
- [ExecuteDelete](/saving/execute-delete) — how to delete rows directly in the database without loading or tracking entities
- [Adding data via the DbContext](/dbcontext/adding-data) — another way to work with entities through the `DbContext` API
- [Bulk Delete](https://entityframework-extensions.net/bulk-delete) — how to delete large datasets with Entity Framework Extensions

## FAQ

### Do Remove and RemoveRange delete rows immediately?

No. `Remove()` and `RemoveRange()` only mark entities as `Deleted`.

The database rows are deleted when `SaveChangesAsync()` runs.

### What is the difference between Remove and RemoveRange?

`Remove()` is used for one entity.

`RemoveRange()` is used for multiple entities.

Both methods use the normal EF Core `ChangeTracker` and `SaveChangesAsync()` workflow.

### Can I delete an entity without loading it first?

Yes. If you already know the key value, you can create an entity instance with only the key and pass it to `Remove()`.

EF Core can then track that entity as `Deleted` and send the delete operation when `SaveChangesAsync()` runs.

### Do I need Attach before Remove?

Not always.

If you pass a disconnected entity to `Remove()`, EF Core can start tracking it as `Deleted`.

Some codebases use `Attach()` first to make the tracking step explicit, then call `Remove()`. Both patterns rely on EF Core knowing the key value of the entity to delete.

### Is RemoveRange the same as BulkDelete?

No. `RemoveRange()` is not a true bulk delete operation.

`RemoveRange()` marks multiple entities as deleted, and `SaveChangesAsync()` sends the database commands. A real bulk delete library such as Entity Framework Extensions is designed for high-volume delete scenarios.

### When should I use ExecuteDelete instead?

Use `ExecuteDeleteAsync()` when the delete can be expressed as a database-side rule and you do not need to load or track entity instances.

Use `Remove()` or `RemoveRange()` when you are working with entity instances in the normal EF Core saving workflow.

### What happens to related data when I delete an entity?

It depends on the relationship configuration and database constraints.

Related rows may be deleted, foreign key values may be set to `null`, or the database may reject the delete if related rows still exist.
