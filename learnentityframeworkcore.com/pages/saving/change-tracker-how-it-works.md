---
title: Tracking Changes of Entities in EF Core
description: Learn how EF Core tracks entity changes, how ChangeTracker and SaveChanges work together, when tracking helps, when AsNoTracking changes behavior, and the most common pitfalls to avoid.
canonical: /saving/change-tracker-how-it-works
status: Published
lastmod: 2026-04-27
---

# Tracking Changes of Entities in EF Core

Tracking changes is the behavior that allows EF Core to know which entity instances belong to the current `DbContext`, which values changed, and what should be saved when `SaveChangesAsync()` runs.

In most EF Core workflows, you load an entity, modify it, and save the changes.

## Quick Answer

In a tracked query, EF Core automatically keeps the entity associated with the current `DbContext`.

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .FirstAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await db.SaveChangesAsync();
```

In this example:

- the product is loaded by the current `DbContext`
- EF Core tracks it automatically
- the `Price` property is changed in memory
- `SaveChangesAsync()` detects the change and sends the required `UPDATE`

This is the standard tracked workflow in EF Core.

## Common Tracking Scenarios

The following examples show the main tracking behaviors you usually need in EF Core before going deeper into how tracking works internally.

| Scenario | Typical API | Tracking Result |
|---|---|---|
| Query and modify an entity | LINQ query + property change | The entity is tracked and can become `Modified` |
| Track a new entity | `Add()` | The entity is tracked as `Added` |
| Track an entity for deletion | `Remove()` | The entity is tracked as `Deleted` |
| Read without tracking | `AsNoTracking()` | The entity is not tracked |
| Attach a disconnected entity | `Attach()` + `IsModified` | EF Core tracks only what you mark intentionally |

### Update a Tracked Entity

The most common case is loading an entity, changing one or more properties, and saving the change.

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .FirstAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await db.SaveChangesAsync();
```

Because the entity was loaded by the same `DbContext`, EF Core can track the change and save it.

For the full save pipeline, see [SaveChanges](/saving/save-changes).

### Track a New Entity as Added

When you call `Add()`, EF Core starts tracking the entity as `Added`.

```csharp
using var db = new AppDbContext();

db.Products.Add(new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
});

await db.SaveChangesAsync();
```

When `SaveChangesAsync()` runs, EF Core generates an `INSERT`.

For more insertion patterns, see [Adding Data](/saving/adding-data).

### Track an Entity as Deleted

When you load an entity and call `Remove()`, EF Core tracks it as `Deleted`.

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .FirstAsync(p => p.Name == "27-inch Monitor");

db.Products.Remove(product);

await db.SaveChangesAsync();
```

When `SaveChangesAsync()` runs, EF Core generates a `DELETE`.

For more deletion patterns, see [Deleting Data](/saving/deleting-data).

### Query Without Tracking

`AsNoTracking()` tells EF Core not to track the returned entities.

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .AsNoTracking()
    .FirstAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await db.SaveChangesAsync();
```

In this case, no update is sent to the database because the entity is not tracked by the current `DbContext`.

Use `AsNoTracking()` for read-only queries, not for entities you expect to modify and save through the same context.

### Attach a Disconnected Entity

Sometimes an entity was not loaded by the current `DbContext`, but you still need to save a change.

```csharp
using var db = new AppDbContext();

var product = new Product
{
    Id = 10,
    Price = 45
};

db.Attach(product);
db.Entry(product).Property(p => p.Price).IsModified = true;

await db.SaveChangesAsync();
```

This pattern is useful when you want to update only specific properties of a disconnected entity.

For broader update patterns, see [Updating Data](/saving/modifying-data).

## Tracking Changes vs ChangeTracker

Tracking changes is the behavior. The `ChangeTracker` is the EF Core component that stores tracking information such as entity states, original values, current values, and pending changes.

This article focuses on the behavior. For the component itself, see [ChangeTracker](/saving/change-tracker).

## Entity States

Every tracked entity has a state. That state helps EF Core decide what should happen when `SaveChangesAsync()` runs.

### `Detached`

The entity is not tracked by the current `DbContext`.

Typical examples include:

- an object created in memory but never attached
- an entity returned from an `AsNoTracking()` query
- an entity loaded by a different context instance
- a DTO or API payload mapped into a new object

A detached entity is invisible to `SaveChangesAsync()` unless you attach it or mark its state explicitly.

### `Unchanged`

The entity is tracked, but EF Core currently sees nothing to save.

This is the usual state of an entity immediately after a regular tracked query.

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .FirstAsync(p => p.Name == "Wireless Mouse");

// product is tracked as Unchanged
```

### `Modified`

The entity is tracked and EF Core knows that one or more values changed.

```csharp
product.Price = 45;
```

When `SaveChangesAsync()` runs, this usually leads to an `UPDATE`.

### `Added`

The entity is tracked as new.

```csharp
db.Products.Add(new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
});
```

When `SaveChangesAsync()` runs, this usually leads to an `INSERT`.

### `Deleted`

The entity is tracked for deletion.

```csharp
db.Products.Remove(product);
```

When `SaveChangesAsync()` runs, this usually leads to a `DELETE`.

These states are not just labels. They directly affect the SQL commands EF Core generates.

## How EF Core Detects Changes

Tracking an entity is only part of the process. EF Core also needs to know what actually changed.

In a typical tracked workflow, EF Core:

1. loads or attaches an entity
2. starts tracking the entity instance
3. keeps state and value information internally
4. detects changes before saving
5. marks the entity or specific properties as modified
6. generates the appropriate SQL when `SaveChangesAsync()` runs

For example:

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .FirstAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await db.SaveChangesAsync();
```

EF Core does not immediately send an update when `product.Price` changes.

The database command is generated when `SaveChangesAsync()` runs.

> Tip: If you are not sure whether EF Core detected a change, enable EF Core logging during development and check whether `SaveChangesAsync()` generated an `INSERT`, `UPDATE`, or `DELETE`.

Use sensitive-data logging only in development.

## When Tracking Is Lost

Tracking only exists within the lifetime of the current `DbContext`.

An entity is not tracked by the saving context when:

- it was returned from an `AsNoTracking()` query
- it was loaded by a different `DbContext`
- it came from an API request or DTO
- the original context was already disposed
- the entity was manually detached

Example:

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .AsNoTracking()
    .FirstAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await db.SaveChangesAsync();
```

This does not update the database because the entity is not tracked.

If you need to save a disconnected entity, you usually need to attach it and control its state intentionally.

```csharp
using var db = new AppDbContext();

var product = new Product
{
    Id = 10,
    Price = 45
};

db.Attach(product);
db.Entry(product).Property(p => p.Price).IsModified = true;

await db.SaveChangesAsync();
```

This tells EF Core exactly which property should be treated as modified.

## Why Disconnected Entities Need Explicit Tracking

Tracking is easiest to understand in a connected scenario.

A connected scenario means the same `DbContext` loads the entity, tracks it, detects the change, and saves it.

```csharp
using var db = new AppDbContext();

var product = await db.Products
    .FirstAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await db.SaveChangesAsync();
```

This is the normal tracked workflow.

A disconnected scenario is different. The entity was not loaded by the same `DbContext` that later saves it. This can happen with API payloads, DTOs, another context instance, or an entity returned from `AsNoTracking()`.

In those cases, EF Core needs more explicit instructions, such as attaching the entity or marking specific properties as modified.

For a dedicated explanation of the connected flow, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

## Performance Considerations

Tracking is useful, but it has a cost.

The cost becomes more noticeable when:

- many entities are tracked in the same context
- the context lives longer than necessary
- large graphs remain attached without a reason
- change detection runs over a large tracked set
- the workload is read-heavy instead of update-heavy

For read-only queries, use `AsNoTracking()`:

```csharp
using var db = new AppDbContext();

var products = await db.Products
    .AsNoTracking()
    .Where(p => p.Price > 100)
    .ToListAsync();
```

This avoids tracking entities that you do not plan to modify.

Practical guidance:

- use normal tracking when you intend to update entities in the same unit of work
- use `AsNoTracking()` for read-only queries
- keep `DbContext` lifetimes short
- avoid keeping large tracked graphs alive unnecessarily
- avoid long-lived contexts unless you have a specific reason

Tracking is not a problem by itself. The issue is unnecessary tracking in workflows where you never plan to save those entities.

## Common Pitfalls

Tracking issues often come from incorrect assumptions.

Be careful when:

- using `AsNoTracking()` and still expecting automatic updates
- expecting `SaveChangesAsync()` to update detached entities
- calling `Update()` on an entity that is already tracked
- marking an entire disconnected entity as modified when only one field changed
- keeping too many tracked entities alive in a long-lived context
- mixing tracked and detached entities without a clear workflow

A useful rule of thumb is:

> If the entity was loaded and modified by the same `DbContext`, EF Core can usually track the change naturally. If the entity came from somewhere else, you need to be explicit.

## When to Use Tracking

Use normal tracking when:

- you query and update entities within the same `DbContext`
- you are working in a connected scenario
- you want EF Core to detect changes automatically
- you are implementing standard CRUD workflows
- you want a simple unit-of-work flow

Tracking is usually the clearest option for ordinary insert, update, and delete scenarios.

## When Not to Use Tracking

Avoid unnecessary tracking when:

- the query is read-only
- you are loading large result sets
- you do not plan to call `SaveChangesAsync()` for those entities
- the context would keep too many entities alive
- the operation does not need entity state management

For read-only queries, prefer `AsNoTracking()`.

The goal is not to disable tracking everywhere. The goal is to use tracking when EF Core needs to remember entity state, and avoid it when the result is only being read.

## External Resources - Tracking Changes

The following videos are useful if you want to reinforce how EF Core tracks entity changes in practice, how entity states evolve during a unit of work, and how tracking behavior affects `SaveChangesAsync()` and performance.

### Video 1 - How does EF Core keep track of changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk?si=NBPmjARYNj9vmZmI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This is one of the strongest technical companions for this topic because it explains how EF Core uses the `ChangeTracker` internally, how entity states change, and why `AsNoTracking()` or unnecessary `Update()` calls can change the expected save behavior.

Key timestamps:

- [0:30](https://www.youtube.com/watch?v=uXDYEBexlYk&t=30) — Introduction to `ChangeTracker` and entity states
- [2:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=120) — Delete flow (`Unchanged` → `Deleted` → `Detached`)
- [3:30](https://www.youtube.com/watch?v=uXDYEBexlYk&t=210) — `Update()` and `AsNoTracking()` pitfalls
- [7:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=420) — Code demo: `Update()` vs. tracked flow with SQL logs

### Video 2 - Don't SUCK With Entity Framework - Change Tracking - Performance Tips Part 8

<iframe width="560" height="315" src="https://www.youtube.com/embed/X9un5y7073c?si=OAoDFcqpSN5v_1C3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video is the most performance-oriented resource in the set. It shows why tracking has a cost, when `AsNoTracking()` helps, and how query tracking behavior can affect read-heavy EF Core workloads.

Key timestamps:

- [0:30](https://www.youtube.com/watch?v=X9un5y7073c&t=30) — What change tracking is and when it starts automatically
- [2:00](https://www.youtube.com/watch?v=X9un5y7073c&t=120) — `AsNoTracking()` demo and tracked-entity count comparison
- [4:00](https://www.youtube.com/watch?v=X9un5y7073c&t=240) — `QueryTrackingBehavior` in `DbContext`
- [7:00](https://www.youtube.com/watch?v=X9un5y7073c&t=420) — Tracking in projections vs. full entity materialization

### Video 3 - Change Tracker in Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/pC2XbknnOeY?si=6JWtdGK-r3Wzugdm" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video is a practical walkthrough of the five main `ChangeTracker` states: `Detached`, `Added`, `Unchanged`, `Modified`, and `Deleted`, with examples that connect those states to common save workflows.

Key timestamps:

- [0:45](https://www.youtube.com/watch?v=pC2XbknnOeY&t=45) — Introduction to change tracking and the five states
- [3:30](https://www.youtube.com/watch?v=pC2XbknnOeY&t=210) — Demo: `Unchanged` → `Modified`
- [6:00](https://www.youtube.com/watch?v=pC2XbknnOeY&t=360) — Demo: `Detached` → `Added`
- [10:30](https://www.youtube.com/watch?v=pC2XbknnOeY&t=630) — `AsNoTracking()` and read-performance optimization

## Summary

Tracking changes is how EF Core connects object changes in memory with database commands generated later by `SaveChangesAsync()`.

Key points:

- regular entity queries are tracked by default
- `AsNoTracking()` disables tracking for read-only results
- entity state determines whether EF Core inserts, updates, deletes, or ignores an entity
- connected scenarios are simpler because the same `DbContext` tracks and saves the entity
- disconnected entities require explicit tracking or state configuration

## Related Articles

If you want to explore the most closely related saving and tracking topics, these pages are the best next step:

- [ChangeTracker](/saving/change-tracker) — how EF Core stores tracking information internally
- [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
- [Saving Data in Connected Scenario](/saving/save-changes-connected-entities) — how tracked entities are saved in a normal connected flow
- [Updating Data](/saving/modifying-data) — how EF Core updates existing entities
- [Adding Data](/saving/adding-data) — how new entities become tracked as `Added`

## FAQ

### What does it mean for an entity to be tracked in EF Core?

It means the current `DbContext` is monitoring that entity instance and can use its state when `SaveChangesAsync()` runs.

### Are EF Core queries tracked by default?

Yes. Regular entity queries are tracked by default unless you use `AsNoTracking()` or configure no-tracking behavior globally.

### Why did `SaveChangesAsync()` not update my entity?

A common reason is that the entity was not tracked by the current `DbContext`. This can happen with `AsNoTracking()`, another context instance, a DTO, or a disposed context.

### What does `AsNoTracking()` change?

`AsNoTracking()` tells EF Core not to track the entities returned by that query. This is useful for read-only queries.

### Should I call `Update()` on a tracked entity?

Usually no. If the entity was loaded and modified by the same `DbContext`, EF Core is already tracking it. `Update()` is more commonly useful with disconnected entities.