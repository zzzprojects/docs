---
title: Tracking Changes of Entities in EF Core
description: Learn how EF Core tracks entity changes, how ChangeTracker and SaveChanges work together, when tracking helps, when AsNoTracking changes behavior, and the most common pitfalls to avoid.
canonical: /saving/deleting-data
status: Published
lastmod: 2026-04-07
---

# Tracking Changes of Entities in EF Core

Tracking changes is one of the core behaviors behind everyday EF Core workflows.

It is what allows EF Core to understand **which entities are being monitored**, **what changed since they were loaded or attached**, and **what should be persisted** when `SaveChanges()` runs.

In practice, this topic sits at the intersection of three related concepts:

- ChangeTracker → /changetracker
- SaveChanges → /savechanges
- Saving Data in Connected Scenario → /saving-data-in-connected-scenario

This article focuses on the tracking side of that relationship: how entities become tracked, how EF Core detects changes, why tracking is convenient in connected scenarios, and why it often becomes more delicate in disconnected ones.

## TL;DR — Tracking Changes in EF Core

- EF Core tracks entities within the lifetime of the current `DbContext`
- regular queries are tracked by default
- `AsNoTracking()` disables tracking for query results
- tracked entities can be modified and persisted naturally through `SaveChanges()`
- entity state is central to how EF Core decides what SQL to generate
- tracking is usually a good fit for connected update scenarios
- misunderstood tracking is one of the most common causes of missing updates, unexpected updates, and avoidable overhead

## Quick Mental Model

Think of `DbContext` as a temporary unit of work.

When an entity is tracked, EF Core keeps enough internal information to answer questions like:

- Is this entity new?
- Was it modified?
- Should it be updated?
- Should it be inserted?
- Should it be deleted?
- Is it not part of this context at all?

`SaveChanges()` depends on that tracking information. If EF Core does not know that something changed, there is nothing to persist.

## What Does Tracking Changes Mean in EF Core

Tracking changes means EF Core keeps an entity associated with the current `DbContext` and monitors its state over time.

At a high level, EF Core can:

- keep a reference to an entity instance
- assign an entity state
- compare current values with original values when needed
- detect which entities or properties changed
- use that information to generate `INSERT`, `UPDATE`, or `DELETE` commands

If an entity is not tracked, changing it in memory does not automatically mean anything will happen in the database.

## How EF Core Starts Tracking Entities

EF Core commonly starts tracking entities when:

- you load them through a regular query
- you add them with `Add()`
- you attach them with `Attach()`
- you mark them for update with `Update()`
- you mark them for deletion with `Remove()`

A typical tracked update looks like this:

```csharp
using var db = new AppDbContext();

var product = db.Products.First();

product.Price = 150;

db.SaveChanges();
```

This works because:

* the entity was loaded by the current `DbContext`
* EF Core tracks it automatically
* the property change is detected
* `SaveChanges()` persists the update

This is the most natural EF Core workflow and the one most developers expect.

## Entity States

Every tracked entity is associated with a state, and that state helps EF Core decide what should happen when changes are saved.

### `Detached`

The entity is not tracked by the current `DbContext`.

Typical examples include:

* an object created in memory but never attached
* an entity returned from a no-tracking query
* an entity materialized by a different context
* an object received from outside the current unit of work

### `Unchanged`

The entity is tracked, but EF Core currently sees nothing to persist.

This is typically the state of an entity immediately after a regular query.

### `Modified`

The entity is tracked and EF Core knows that one or more values changed.

This usually leads to an `UPDATE` when `SaveChanges()` runs.

### `Added`

The entity is tracked as new.

This usually leads to an `INSERT`.

### `Deleted`

The entity is tracked for deletion.

This usually leads to a `DELETE`.

These states are not just descriptive. They directly influence the commands EF Core generates.

## Tracked Queries vs No-Tracking Queries

One of the most important distinctions in EF Core is whether query results are tracked.

### Tracked query (default behavior)

```csharp
using var db = new AppDbContext();

var product = db.Products.First();
product.Price = 150;

db.SaveChanges();
```

This produces an update because the entity is tracked.

### No-tracking query

```csharp
using var db = new AppDbContext();

var product = db.Products
    .AsNoTracking()
    .First();

product.Price = 150;

db.SaveChanges();
```

This does not produce an update because the entity is not tracked.

That difference is small in code, but huge in behavior.

Use `AsNoTracking()` when you are reading data and do not intend to update those entities through the same `DbContext`.

## How EF Core Detects Changes

Tracking an entity is only part of the story. EF Core also needs to determine what actually changed.

In typical workflows, EF Core:

1. tracks the entity instance
2. keeps original values or equivalent tracking metadata
3. compares current values when change detection runs
4. marks the entity or specific properties as modified
5. uses that information when `SaveChanges()` generates SQL

In other words, EF Core does not update everything blindly in normal tracked scenarios. It uses change tracking information to decide what needs to be persisted.

## Explicit State Control

Sometimes the current `DbContext` did not load the entity, but you still need to update it.

That usually means you must attach the entity and control state explicitly.

```csharp
using var db = new AppDbContext();

var product = new Product { Id = 10, Price = 150 };

db.Attach(product);
db.Entry(product).Property(p => p.Price).IsModified = true;

db.SaveChanges();
```

This pattern is especially useful in disconnected scenarios where you want to update only specific properties rather than treating the entire entity as modified.

## Connected Scenario

A connected scenario means the entity is queried and updated within the same `DbContext`.

```csharp
using var db = new AppDbContext();

var product = db.Products.First();
product.Price += 10;

db.SaveChanges();
```

Why this is usually predictable:

* the entity is already tracked
* original and current values belong to the same unit of work
* EF Core has the context it needs to detect changes naturally
* no manual attach logic is required

This is where tracking shines. It keeps updates simple and reduces the need for manual state management.

For a dedicated walkthrough of that flow, see [Saving Data in Connected Scenario](/saving-data-in-connected-scenario).

## Disconnected Scenario

A disconnected scenario means the entity reaches the current `DbContext` from somewhere else.

Typical examples:

* a DTO from an API request
* data from a form post
* an entity created or loaded in a previous context
* work that crosses application/service boundaries

Example:

```csharp
using var db = new AppDbContext();

var product = new Product
{
    Id = 10,
    Name = "Keyboard",
    Price = 150
};

db.Attach(product);
db.Entry(product).State = EntityState.Modified;

db.SaveChanges();
```

This works, but it is more delicate than the connected case.

Why?

* the entity was not originally tracked by this context
* EF Core has less context about what actually changed
* broad state assignment can mark more data as modified than intended
* related graphs become harder to manage correctly
* careless use of `Update()` or `EntityState.Modified` can generate wider updates than expected

This is one of the main reasons disconnected workflows often feel more error-prone than connected ones.

## Generated SQL and Observability

Tracking itself is internal behavior, but its effects are visible through generated SQL and logging.

A practical setup during development is:

```csharp
var options = new DbContextOptionsBuilder<AppDbContext>()
    .LogTo(Console.WriteLine, LogLevel.Information)
    .EnableSensitiveDataLogging()
    .Options;

using var db = new AppDbContext(options);

var product = db.Products.First();
product.Price = 150;

db.SaveChanges();
```

Logging helps verify things such as:

* whether an `UPDATE` was generated at all
* whether more columns were updated than expected
* whether an entity was treated as modified
* whether a disconnected update was broader than intended

Use sensitive-data logging only in development.

## Performance Considerations

Tracking is useful, but it is not free.

The cost becomes more noticeable when:

* many entities are tracked in the same context
* contexts live longer than they should
* large graphs remain attached unnecessarily
* change detection runs over a large tracked set
* the workload is read-heavy rather than update-heavy

Practical guidance:

* use normal tracking when you intend to update entities in the same unit of work
* use `AsNoTracking()` for read-only queries
* keep `DbContext` lifetimes reasonably short
* avoid keeping large tracked graphs around without a reason
* do not assume tracking is harmless in high-volume read scenarios

### Read-only example

```csharp
using var db = new AppDbContext();

var products = db.Products
    .AsNoTracking()
    .Where(x => x.Price > 100)
    .ToList();
```

In read-heavy scenarios, this often makes more sense than tracking entities you never plan to modify.

### Bulk note

If you need to process or persist very large numbers of entities, standard change tracking plus standard `SaveChanges()` may become too expensive.

In those cases, reducing unnecessary tracking or using bulk-oriented approaches is often more appropriate than relying on one very large tracked graph.

## When to Use Tracking vs When to Avoid It

Use tracking when:

* you load entities and update them within the same `DbContext`
* you are working in connected scenarios
* you want EF Core to detect changes automatically
* you are implementing standard CRUD with a normal unit-of-work flow

Be careful or avoid naive usage when:

* you use `AsNoTracking()` and later expect automatic persistence
* you keep a context alive too long
* you attach disconnected entities without controlling state explicitly
* you use `Update()` broadly without considering its impact
* you apply tracking to large read-only workloads where it adds cost but no value

## Common Pitfalls

Tracking issues often come from assumptions rather than syntax errors.

Common examples include:

* assuming queried entities are always tracked, even when `AsNoTracking()` was used
* expecting `SaveChanges()` to update detached entities automatically
* marking an entire disconnected entity as modified when only one field changed
* keeping too many tracked entities alive in a long-lived context
* mixing tracked and detached entities without a clear workflow

These mistakes are common because EF Core often looks simple on the surface, but tracking behavior depends heavily on context lifetime and entity state.

## External Resources — Tracking Changes

The following videos are especially useful if you want to reinforce how EF Core tracks entity changes in practice, how entity states evolve during a unit of work, and why tracking behavior directly affects `SaveChanges()`, SQL generation, and performance.

Together, they help cover three complementary angles:

- how the **ChangeTracker** works internally
- how tracking impacts **performance**
- how entity states and `SaveChanges()` interact in real code examples

### Video 1 — [How does EF Core keeps track of changes?](https://www.youtube.com/watch?v=uXDYEBexlYk)

This is one of the strongest technical companions for this topic.

It breaks down how EF Core uses the **ChangeTracker** internally, with a strong focus on entity states such as `Added`, `Unchanged`, `Deleted`, and `Detached`. It also shows how `AsNoTracking()` changes behavior and why calling `Update()` unnecessarily can lead to broader updates than expected, with possible performance and concurrency consequences.

It is especially useful if you want to better understand:

- how state transitions happen before and after `SaveChanges()`
- why tracked and non-tracked entities behave differently
- why `Update()` can be overused in connected scenarios
- how SQL logging helps reveal what EF Core is actually doing

Key timestamps:

- **~0:30** — Introduction to `ChangeTracker` and entity states
- **~2:00** — Delete flow (`Unchanged` → `Deleted` → `Detached`)
- **~3:30** — `Update()` and `AsNoTracking()` pitfalls
- **~7:00** — Code demo: `Update()` vs. tracked flow with SQL logs

### Video 2 — [Don't SUCK With Entity Framework - Change Tracking - Performance Tips Part 8](https://www.youtube.com/watch?v=X9un5y7073c&t=472s)

This is the most performance-oriented resource in the set.

It demonstrates the real cost of change tracking in EF Core, including benchmarks that show how `AsNoTracking()` can significantly improve read-only query performance. It is a great companion to this article because it moves the discussion from theory to practical decision-making: when tracking is useful, when it is automatic, and when it simply adds overhead.

It is especially useful if you want to understand:

- when EF Core starts tracking entities automatically
- why read-only queries often benefit from `AsNoTracking()`
- how global `QueryTrackingBehavior` affects application behavior
- why projections and entity materialization do not behave the same way

Key timestamps:

- **~0:30** — What change tracking is and when it starts automatically
- **~2:00** — `AsNoTracking()` demo and tracked-entity count comparison
- **~4:00** — `QueryTrackingBehavior` in `DbContext`
- **~7:00** — Tracking in projections vs. full entity materialization

### Video 3 — [Change Tracker in Entity Framework Core](https://www.youtube.com/watch?v=pC2XbknnOeY)

This is a very useful practical walkthrough of the five main `ChangeTracker` states: `Detached`, `Added`, `Unchanged`, `Modified`, and `Deleted`.

It works well as a visual companion because it shows those states in action through live examples, including entity creation, modification, and optimization choices such as `AsNoTracking()` for read-heavy workflows. It also includes an override of `SaveChanges()`, which makes it a helpful bridge between tracking behavior and auditing scenarios.

It is especially useful if you want to see:

- how the five states appear in real code
- how an entity moves from `Unchanged` to `Modified`
- how new entities move from `Detached` to `Added`
- how `AsNoTracking()` affects entity state and tracking overhead

Key timestamps:

- **~0:45** — Introduction to change tracking and the five states
- **~3:30** — Demo: `Unchanged` → `Modified`
- **~6:00** — Demo: `Detached` → `Added`
- **~10:30** — `AsNoTracking()` and read-performance optimization

## Summary & Next Steps

Tracking changes is one of the behaviors that makes EF Core feel natural in ordinary update workflows.

The better you understand:

* `DbContext` lifetime
* tracked vs detached entities
* entity state
* no-tracking queries
* connected vs disconnected flows

the easier it becomes to predict what `SaveChanges()` will actually do.

Next steps:

* ChangeTracker → /changetracker
* SaveChanges → /savechanges
* Saving Data in Connected Scenario → /saving-data-in-connected-scenario

## FAQ

**What does it mean for an entity to be tracked in EF Core?**
It means the current `DbContext` is monitoring that entity and can use its state and change information when `SaveChanges()` runs.

**Are EF Core queries tracked by default?**
Yes. Regular queries are tracked by default unless you opt out with `AsNoTracking()`.

**Why didn’t `SaveChanges()` update my entity?**
A common reason is that the entity was not tracked by the current `DbContext`, or the relevant property was not marked as modified.

**What does `AsNoTracking()` change?**
It tells EF Core not to track entities returned by that query, which is often preferable for read-only workloads.

**Is tracking always good?**
No. Tracking is useful for update workflows, but it adds overhead that may be unnecessary in read-only scenarios.

**Why can disconnected updates be risky?**
Because the current context did not observe the original entity lifecycle, so broad state assignment can update more data than intended.

**Does tracking automatically mean only changed columns are updated?**
Not in every possible workflow. The exact behavior depends on how the entity became tracked and how its state/properties were marked.

```