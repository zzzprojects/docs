---
title: Saving data in disconnected scenarios
description: An examination of how to save entities that are not tracked by the current DbContext in Entity Framework Core
canonical: /saving/disconnected-entities
status: Published
lastmod: 2026-06-01
---

# EF Core Disconnected Entities

A disconnected entity is an entity that is not being tracked by the current `DbContext`.

Use methods such as `AddRange()`, `UpdateRange()`, or `RemoveRange()` to tell EF Core whether disconnected entities should be inserted, updated, or deleted when [`SaveChangesAsync()`](/saving/save-changes) is called.

## Insert Disconnected Entities with AddRange

Use `Add` or `AddRange()` when the disconnected entities are new and should be inserted.

```csharp
using var context = new AppDbContext();

var keyboard = new Product { Name = "Mechanical Keyboard", Price = 120 };
var mouse = new Product { Name = "Wireless Mouse", Price = 40 };
var monitor = new Product { Name = "27-inch Monitor", Price = 250 };

context.Products.AddRange(keyboard, mouse, monitor);

await context.SaveChangesAsync();
```

This inserts the three products in the database.

`AddRange()` tells EF Core to track the entities as `Added`.

For more details about this insert workflow, see [Adding Data](/saving/adding-data).

## Update Disconnected Entities with UpdateRange

Use `Update` or `UpdateRange()` when the disconnected entities already exist in the database and should be updated.

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

This marks all three products and all their properties as `Modified`.

As a result, EF Core assumes every property has changed and includes all mapped columns in the generated `UPDATE` statement, even if only some values were actually changed.

`UpdateRange()` is useful for disconnected workflows, but it does not perform change tracking or determine which properties changed.

For more details about this update workflow, see [Updating Data](/saving/modifying-data).

## Update Selected Properties with AttachRange

We just saw that `Update` and `UpdateRange()` mark all properties as `Modified`.

If you want to update only specific properties, you can use `Attach` or `AttachRange()` and explicitly mark the properties that should be updated.

```csharp
using var context = new AppDbContext();

var product = new Product
{
    Id = 10,
    Price = 45
};

context.Products.Attach(product);

context.Entry(product)
    .Property(p => p.Price)
    .IsModified = true;

await context.SaveChangesAsync();
```

This attaches the entity and marks only the `Price` property as `Modified`.

As a result, EF Core generates an `UPDATE` statement that only includes the `Price` column.

This approach is useful in disconnected scenarios when you know exactly which properties have changed and want to avoid updating every column.

For more details about this update workflow, see [Updating Data](/saving/modifying-data).

## Delete Disconnected Entities with RemoveRange

Use `Remove` or `RemoveRange()` when the disconnected entities should be deleted.

```csharp
using var context = new AppDbContext();

var keyboard = new Product { Id = 1 };
var mouse = new Product { Id = 2 };
var monitor = new Product { Id = 3 };

context.Products.RemoveRange(keyboard, mouse, monitor);

await context.SaveChangesAsync();
```

This deletes the three products from the database when `SaveChangesAsync()` runs.

`RemoveRange()` tells EF Core to track the entities as `Deleted`.

For more details about this delete workflow, see [Deleting Data](/saving/deleting-data).

## What Are Disconnected Entities?

EF Core automatically tracks entities returned from the database by a `DbContext`.

In a [connected workflow](/saving/save-changes-connected-entities), the same context queries the entity, tracks it, detects changes, and saves those changes.

In a disconnected workflow, the entity is saved by a `DbContext` that was not already tracking it.

This is common in web applications and APIs, where data is sent to a client, modified, sent back to the server, and then saved using a new context instance.

## AddRange, UpdateRange, and RemoveRange in Disconnected Scenarios

In disconnected scenarios, the method you choose tells EF Core which state should be assigned to the entity.

* `AddRange()` tracks entities as `Added`
* `UpdateRange()` tracks entities as `Modified`
* `RemoveRange()` tracks entities as `Deleted`

The database operation is sent only when `SaveChangesAsync()` runs.

For more details about range methods such as `AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`, see the official EF Core documentation on [range methods](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange).

## Important Behavior

Disconnected entities are not being tracked by the current `DbContext`.

That means EF Core does not already know whether they are new, changed, or should be deleted. You need to choose the method that matches the intended operation:

* use `AddRange()` for new entities
* use `UpdateRange()` for existing entities that should be updated
* use `RemoveRange()` for entities that should be deleted

After that, `SaveChangesAsync()` sends the required `INSERT`, `UPDATE`, or `DELETE` statements to the database.

A key value is especially important for updates and deletes. Without a valid key, EF Core cannot reliably target the existing row.

EF Core can only track one instance of an entity with a given primary key value in the same `DbContext`. Using a short-lived context for each unit of work helps avoid tracking conflicts.

For a deeper explanation of how tracked changes are persisted, see [SaveChanges](/saving/save-changes).

For more about how EF Core stores entity states, see [ChangeTracker](/saving/change-tracker).

For the official EF Core documentation about disconnected scenarios, see [Disconnected entities](https://learn.microsoft.com/en-us/ef/core/saving/disconnected-entities).

## How EF Core Handles Disconnected Entities

When a disconnected entity arrives in a new `DbContext`, EF Core has no previous tracking information for that instance.

A simplified flow looks like this:

1. The entity is created, received, or mapped from another layer.
2. A new `DbContext` starts tracking it.
3. You call `AddRange()`, `UpdateRange()`, or `RemoveRange()`.
4. EF Core marks the entity as `Added`, `Modified`, or `Deleted`.
5. You call `SaveChangesAsync()`.
6. EF Core sends the corresponding database command.

The method you call is the signal EF Core uses to decide the entity state.

## When to Use Disconnected Entity Patterns

Use disconnected entity patterns when:

* data is sent from a client and saved later by a new `DbContext`
* you are working in a web application or API
* the original context that loaded the data is no longer available
* you already know whether the incoming entities should be inserted, updated, or deleted
* you want to save one or more incoming entities as part of the same unit of work

Common examples include:

* inserting several new products from a request
* updating products received from an API payload
* deleting selected products by key
* saving changes from a service layer where the original query context has already been disposed

## When Not to Use Disconnected Entity Patterns

Disconnected patterns are not always the best choice.

Avoid them when:

* the entity is already tracked by the current `DbContext`
* you only need to update or delete rows that match a query
* you need high-performance operations for thousands or millions of rows
* you only want to update one specific property
* you are not sure whether the entity is new or existing

For connected workflows, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

For set-based updates, see [ExecuteUpdate](/saving/execute-update).

For set-based deletes, see [ExecuteDelete](/saving/execute-delete).

For high-volume operations, see [Bulk Insert](https://entityframework-extensions.net/bulk-insert), [Bulk Update](https://entityframework-extensions.net/bulk-update), and [Bulk Delete](https://entityframework-extensions.net/bulk-delete).

## Common Pitfalls

Be careful with the following mistakes.

### Using AddRange for Existing Entities

`AddRange()` tells EF Core that the entities are new.

If the entities already exist in the database, EF Core may try to insert duplicate rows.

Use `UpdateRange()` when the entities already exist and should be updated.

### Using UpdateRange When Only One Property Changed

`UpdateRange()` marks entities as `Modified`.

In disconnected scenarios, EF Core usually does not know which properties actually changed before the entity was attached.

If you only want to update one property, use a more explicit pattern such as `Attach()` with `Property(...).IsModified = true`.

For more information, see [Updating Data](/saving/modifying-data).

### Deleting Entities Without a Key

`RemoveRange()` needs valid key values to know which rows should be deleted.

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

For more information, see [Deleting Data](/saving/deleting-data).

### Tracking Multiple Instances with the Same Key

EF Core can only track one instance of an entity with a given primary key value in the same `DbContext`.

This can happen if one instance is already tracked and another instance with the same key is attached later.

A short-lived `DbContext` per unit of work helps reduce this problem.

### Forgetting SaveChangesAsync

`AddRange()`, `UpdateRange()`, and `RemoveRange()` only change entity states in the `ChangeTracker`.

You must call `SaveChangesAsync()` to persist the insert, update, or delete operation in the database.

## External Resources - Disconnected Entities

The following resources were selected because each one supports an important part of the disconnected entity workflow: what disconnected entities are, how EF Core assigns entity states, and why the `ChangeTracker` matters when a new `DbContext` saves incoming entities.

### Video 1 - Eager Loading, Lazy Loading, Explicit Loading, and Disconnected Entities

<iframe width="560" height="315" src="https://www.youtube.com/embed/rvWQxoPXKuc?start=6960" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This Dot Net Tutorials video covers several EF Core topics, but the most relevant part for this article starts near the end, where it explains disconnected entities and saving disconnected data.

That section shows how EF Core can work with entities that are not already tracked by the current `DbContext`, and how states such as `Added`, `Modified`, and `Deleted` can be assigned before saving changes.

It is useful as a complementary resource if you want to see a practical explanation of disconnected entities without going into advanced graph-tracking scenarios.

**Key sections:**

* [1:56:00](https://www.youtube.com/watch?v=rvWQxoPXKuc&t=6960s) — Introduction to disconnected entities
* [1:56:30](https://www.youtube.com/watch?v=rvWQxoPXKuc&t=6990s) — How a context may not track an entity
* [1:57:00](https://www.youtube.com/watch?v=rvWQxoPXKuc&t=7020s) — Updating a disconnected entity
* [1:58:30](https://www.youtube.com/watch?v=rvWQxoPXKuc&t=7110s) — Using `DbContext.Entry()` with disconnected entities
* [2:00:00](https://www.youtube.com/watch?v=rvWQxoPXKuc&t=7200s) — Setting entity states such as `Added`, `Modified`, and `Deleted`
* [2:02:00](https://www.youtube.com/watch?v=rvWQxoPXKuc&t=7320s) — Using `Add`, `Update`, and `Remove` with disconnected entities

### Video 2 - Change Tracker in Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/pC2XbknnOeY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Rahul Pandey explains how EF Core uses the `ChangeTracker` to manage entity states such as `Detached`, `Added`, `Unchanged`, `Modified`, and `Deleted`.

This is especially useful for disconnected scenarios, where an entity arrives in a new `DbContext` without existing tracking information. The video helps connect methods such as `Add()`, `Update()`, and `Remove()` with the states EF Core assigns before `SaveChanges()` generates the database commands.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=pC2XbknnOeY&t=0s) — Introduction to change tracking in EF Core
* [02:30](https://www.youtube.com/watch?v=pC2XbknnOeY&t=150s) — Entity states such as `Detached`, `Added`, `Modified`, and `Deleted`
* [06:00](https://www.youtube.com/watch?v=pC2XbknnOeY&t=360s) — Practical examples of how states are handled in a `DbContext`
* [10:00](https://www.youtube.com/watch?v=pC2XbknnOeY&t=600s) — When tracking helps and when it may not be needed

## Summary

Disconnected entities are entities that are not being tracked by the current `DbContext`.

Key points:

* disconnected scenarios are common in web applications and APIs
* EF Core needs to know whether the entity should be inserted, updated, or deleted
* use `AddRange()` when disconnected entities are new
* use `UpdateRange()` when disconnected entities already exist and should be updated
* use `RemoveRange()` when disconnected entities should be deleted
* `SaveChangesAsync()` sends the corresponding `INSERT`, `UPDATE`, or `DELETE` statements to the database
* keys are important for updating and deleting existing rows
* EF Core can only track one instance of an entity with a given primary key value in the same `DbContext`

Use disconnected entity patterns when the original context is no longer available and a new context must save incoming entities.

## Related Articles

If you want to understand how disconnected entities fit into the broader EF Core saving workflow, these pages are the best next steps:

* [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
* [Adding Data](/saving/adding-data) — how to insert new disconnected entities with `Add()` or `AddRange()`
* [Updating Data](/saving/modifying-data) — how to update disconnected entities with `Update()` or `UpdateRange()`
* [Deleting Data](/saving/deleting-data) — how to delete disconnected entities with `Remove()` or `RemoveRange()`
* [Saving Data in Connected Scenario](/saving/save-changes-connected-entities) — how EF Core saves entities that are already tracked by the current context
* [ChangeTracker](/saving/change-tracker) — how EF Core stores entity states such as `Added`, `Modified`, and `Deleted`

## FAQ

### What is a disconnected entity in EF Core?

A disconnected entity is an entity that is not being tracked by the current `DbContext`.

This often happens when data is loaded or created in one layer, sent to a client, and later saved using a new `DbContext`.

### How do I insert, update, or delete disconnected entities?

Use the method that matches the intended operation:

* use `Add()` or `AddRange()` for new entities
* use `Update()` or `UpdateRange()` for existing entities that should be updated
* use `Remove()` or `RemoveRange()` for entities that should be deleted

After that, call `SaveChangesAsync()` to persist the changes in the database.

### Why are keys important in disconnected scenarios?

Keys help EF Core identify the row that should be updated or deleted.

Without a valid key, EF Core cannot reliably know which existing row the disconnected entity represents.

### Does UpdateRange only update changed properties?

Not always.

In disconnected scenarios, EF Core usually does not know which properties changed before the entity was attached. `UpdateRange()` can therefore mark the entities as `Modified`.

For partial updates, use a more explicit pattern such as `Attach()` with `Property(...).IsModified = true`.

### Can EF Core track two instances with the same key?

No. EF Core can only track one instance of an entity with a given primary key value in the same `DbContext`.

Using a short-lived `DbContext` for each unit of work helps avoid this problem.