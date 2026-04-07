---
title: The Entity Framework Core ChangeTracker
description: Learn what the ChangeTracker does in Entity Framework Core, how it tracks entity states, how it works with SaveChanges, when tracking is lost, and the most common pitfalls to avoid.
canonical: /saving/deleting-data
status: Published
lastmod: 2026-04-07
---

# The Entity Framework Core ChangeTracker

The `ChangeTracker` is the EF Core component responsible for keeping track of entity instances and their state within a `DbContext`.  
It is one of the core pieces behind normal EF Core saving behavior, because `SaveChanges()` relies on tracked state to know what should be inserted, updated, or deleted.

This page focuses on what the `ChangeTracker` actually does, how it fits into normal EF Core workflows, why it matters so much for connected scenarios, and what common mistakes make tracking behavior feel unpredictable.

Related pages:
- SaveChanges → /savechanges
- Saving Data in Connected Scenario → /saving-data-in-connected-scenario
- Tracking Changes of Entities in EF Core → /tracking-changes-of-entities-in-ef-core

## What Is the ChangeTracker in EF Core

The `ChangeTracker` is the part of EF Core that keeps track of entities loaded or attached to a `DbContext`, along with the state EF Core associates with each one.

At a high level, it allows EF Core to:

- know which entities are currently tracked
- know whether an entity is new, changed, deleted, unchanged, or not tracked
- determine what kind of database command should be generated
- provide the state information that `SaveChanges()` uses when persisting data

The `ChangeTracker` is one of the main reasons connected scenarios feel simple in EF Core: the framework already knows what happened because it has been tracking the entity all along.

## Why the ChangeTracker Matters

The `ChangeTracker` matters because `SaveChanges()` does not guess what happened.  
It works from tracked entity state.

That means the `ChangeTracker` is what allows EF Core to understand:

- which entity changed
- what operation is expected (`INSERT`, `UPDATE`, `DELETE`)
- whether the entity is even part of the current unit of work
- whether a scenario is still truly connected

This is why many problems that look like “SaveChanges issues” are actually tracking issues.

## Entity States

Tracked entities usually move through one of these states:

- `Added`
- `Modified`
- `Deleted`
- `Unchanged`
- `Detached`

These states are important because they directly influence what happens when `SaveChanges()` is called.

For example:

- `Added` → usually results in an `INSERT`
- `Modified` → usually results in an `UPDATE`
- `Deleted` → usually results in a `DELETE`
- `Unchanged` → no database command is generated for that entity
- `Detached` → the entity is not tracked by the current context

The state itself does not execute database commands.  
Instead, it tells EF Core what kind of command should be generated later when `SaveChanges()` runs.

## Basic Example

Here is a simple connected example:

```csharp
using var db = new AppDbContext();

var product = db.Products.First();
product.Price += 10;

db.SaveChanges();
```

In this example:

* the entity is loaded by the current `DbContext`
* the `ChangeTracker` begins tracking it
* EF Core detects the property change
* `SaveChanges()` uses that tracked state to generate the required `UPDATE`

No explicit `Attach()` or `Update()` call is needed here because the entity is already being tracked.

## How the ChangeTracker Fits into a Normal Save Flow

In a normal connected flow, the `ChangeTracker` sits between the entity instance in memory and the database command that EF Core eventually generates.

A simplified sequence looks like this:

1. EF Core loads or attaches an entity
2. The `ChangeTracker` starts tracking that entity
3. One or more property values change
4. EF Core detects those changes
5. `SaveChanges()` uses the tracked state to generate the appropriate SQL

That is why the `ChangeTracker` is tightly connected to the normal EF Core pattern:

* query the entity
* modify it
* call `SaveChanges()`

## Connected vs Not Tracked

The `ChangeTracker` is most useful when the entity remains tracked by the same `DbContext` from query time to save time.

That is the normal connected flow in EF Core:

* query the entity
* modify it
* call `SaveChanges()`

If tracking is lost, the story changes.

For example, an entity may no longer be tracked when:

* the `DbContext` has already been disposed
* the entity came from another context instance
* the query used `AsNoTracking()`
* a DTO or payload was mapped into a new object outside the tracking flow

In those cases, EF Core no longer has the same tracked state to rely on.

## Tracking Queries vs No-Tracking Queries

By default, EF Core tracks entities returned by normal queries.
That is what makes connected updates straightforward.

For example:

```csharp
using var db = new AppDbContext();

var product = db.Products.First(p => p.Id == 10);
product.Price = 150;

db.SaveChanges();
```

In this case, the entity is tracked, so `SaveChanges()` can persist the update normally.

Now compare that with `AsNoTracking()`:

```csharp
using var db = new AppDbContext();

var product = db.Products
    .AsNoTracking()
    .First(p => p.Id == 10);

product.Price = 150;

db.SaveChanges();
```

Here, the entity is not tracked, so EF Core does not automatically persist the change.

This is one of the clearest examples of how tracking directly affects saving behavior.

## Why Update() Can Be Misleading

One of the most common mistakes is calling `Update()` when the entity is already tracked.

For example:

```csharp
db.Update(product);
```

If `product` was already loaded by the current `DbContext`, calling `Update()` usually adds no value and can make the code more misleading.

In many cases, it also hides the real lesson: the `ChangeTracker` was already doing the necessary work.

That is why a normal connected update is often simpler and clearer:

* query the entity
* modify it
* call `SaveChanges()`

This becomes more important when you compare tracked updates with disconnected scenarios, where explicit state-setting patterns may actually be necessary.

## When Tracking Is Lost

Tracking is not permanent.
A scenario stops being truly connected when the entity is no longer tracked by the same `DbContext`.

That can happen when:

* the context is disposed
* the entity comes from another `DbContext`
* `AsNoTracking()` was used
* a detached object or DTO is mapped into a new instance
* the entity is explicitly detached

This distinction matters because code often looks similar on the surface even when the tracking behavior is completely different underneath.

## Common Pitfalls

Be careful when:

* using `AsNoTracking()` and still expecting automatic updates
* calling `Update()` even though the entity is already tracked
* assuming an entity is still tracked after the `DbContext` has been disposed
* keeping a `DbContext` alive too long and accumulating too many tracked entities
* confusing tracked entities with detached objects or DTOs
* mixing entities from different context instances

A useful rule of thumb is:

> If the entity was loaded and is still tracked by the same `DbContext`, EF Core usually does not need explicit attach/state-setting code.

## Performance Considerations

The `ChangeTracker` makes EF Core more convenient, but tracking has a cost.

Performance can be affected when:

* too many entities are tracked in the same context
* the context lives longer than necessary
* large graphs are loaded and kept alive unnecessarily
* `SaveChanges()` is called repeatedly inside tight loops

Practical guidance:

* prefer short-lived contexts for normal units of work
* avoid tracking more data than you actually need
* use no-tracking queries for read-only work
* avoid long-lived contexts unless you have a specific reason
* batch work sensibly instead of saving one entity at a time

This does not make the `ChangeTracker` a problem by itself.
It simply means tracking should be used intentionally.

## When the ChangeTracker Is Most Useful

The `ChangeTracker` is especially useful when:

* you query and modify entities in the same `DbContext`
* you want EF Core to detect changes automatically
* you want ordinary CRUD flows with minimal explicit state management
* you want `SaveChanges()` to work in the most idiomatic EF Core way

This is why the `ChangeTracker` is central to everyday EF Core usage.

## When to Use vs When NOT to Rely on Normal Tracking Flow

Use the normal tracking flow when:

* you query and modify entities within the same `DbContext`
* you want EF Core to manage state automatically
* you are working in a connected scenario
* you want the clearest and most idiomatic update path

Be careful / avoid wrong assumptions when:

* you are working with detached objects or DTOs
* you use `AsNoTracking()` and still expect automatic persistence
* you assume “same request” always means “same `DbContext`”
* you mix entity instances from different contexts
* you expect tracking to remain available after the context lifetime ends

## External Resources — ChangeTracker

The following resources provide deeper insight into how the `ChangeTracker` works in EF Core, how it interacts with `SaveChanges()`, and why tracking behavior is often the real reason behind unexpected updates or missing changes.

They are especially useful for understanding entity states, tracking scope per `DbContext`, why explicit `Update()` can be risky in tracked flows, and how tracking decisions can also affect performance in real applications.

### Video 1 — [How does EF Core keeps track of changes?](https://www.youtube.com/watch?v=uXDYEBexlYk)

Hubert Mijalski breaks down the `ChangeTracker` as the layer between your code and the database, showing how EF Core tracks changes through entity states and then uses that tracked state when `SaveChanges()` runs.  
This is the strongest companion resource for this article because it also highlights one of the most important pitfalls: calling `Update()` unnecessarily can mark all properties as modified, generate broader SQL than needed, and increase concurrency risk.

Key sections:

- 00:00 — Introduction to the `ChangeTracker` and its role in `SaveChanges()`
- 01:00 — Entity states (`Added`, `Unchanged`, `Deleted`, `Detached`) with practical flows
- 03:00 — `AsNoTracking()` and why `Update()` exists for detached scenarios
- 06:00 — `Update()` pitfalls + SQL demo (changed property only vs full entity update)

### Video 2 — [C# Entity Framework - More Advanced Topics on EF Core 5](https://www.youtube.com/watch?v=o9XoiPPP2Lw)

Rainer Stropek provides a visual and debugger-driven explanation of the `ChangeTracker`, making it easier to see how entity states evolve during a normal EF Core workflow.  
This is especially useful if you want a more in-depth look at how EF compares original values with current values, how states transition across the entity lifecycle, and how tracking is isolated per `DbContext`.

Key sections:

- 10:00 — Introduction to the `ChangeTracker` and first state transitions (`Detached` → `Added`)
- 20:00 — Full state lifecycle with `SaveChanges()` (`Detached` → `Added` → `Unchanged` → `Modified` → `Deleted` → `Detached`)
- 33:20 — `OriginalValues` and precise change detection for `UPDATE`
- 55:00 — `AsNoTracking()`, tracking loss, and `DbContext`-scoped tracking behavior

### Video 3 — [ChangeTracker e Performance con Entity Framework Core](https://www.youtube.com/watch?v=J4x-bN3zSN4)

This video adds an important performance angle by showing how the `ChangeTracker` can affect EF Core workloads when many entities are involved.  
It is a valuable companion resource for understanding tracking overhead in inserts and reads, and for seeing practical optimization techniques such as `AddRange`, `AsNoTracking()`, and temporarily disabling `AutoDetectChangesEnabled` in high-volume scenarios.

Key sections:

- 01:30 — Entity and `DbContext` setup
- 04:00 — Benchmark: `Add` vs `AddRange` with 100k inserts
- 07:00 — Disabling `AutoDetectChangesEnabled` for better save performance
- 10:00 — Reads with `AsNoTracking()` and `QueryTrackingBehavior`

## Summary & Next Steps

The `ChangeTracker` is the EF Core component that keeps track of entity instances and their state inside a `DbContext`.
That tracked state is what allows `SaveChanges()` to generate the right database commands during a normal connected save flow.

The better you understand tracking boundaries, entity states, and when tracking is no longer available, the easier it becomes to write predictable EF Core code.

Next steps:

* SaveChanges → /savechanges
* Saving Data in Connected Scenario → /saving-data-in-connected-scenario
* Tracking Changes of Entities in EF Core → /tracking-changes-of-entities-in-ef-core

## FAQ

**What is the ChangeTracker in EF Core?**
It is the EF Core component that keeps track of entity instances and their state within a `DbContext`.

**Does SaveChanges() use the ChangeTracker?**
Yes. In normal tracked scenarios, `SaveChanges()` relies on the state information maintained by the `ChangeTracker`.

**What entity states does the ChangeTracker use?**
The most common states are `Added`, `Modified`, `Deleted`, `Unchanged`, and `Detached`.

**Does AsNoTracking() disable the ChangeTracker?**
For the entities returned by that query, yes in practice: EF Core does not track them, so later changes are not automatically persisted through the normal connected flow.

**Do I need to call Update() on a tracked entity?**
Usually no. If the entity is already tracked by the current `DbContext`, calling `Update()` is often unnecessary.

**Why do some SaveChanges() problems turn out to be tracking problems?**
Because `SaveChanges()` depends on tracked state. If the entity is not tracked, or not tracked by the current context, the behavior changes.

```
