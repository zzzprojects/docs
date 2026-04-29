---
title: The Entity Framework Core ChangeTracker
description: Learn what the ChangeTracker does in Entity Framework Core, how it tracks entity states, how it works with SaveChanges, when tracking is lost, and the most common pitfalls to avoid.
canonical: /saving/change-tracker
status: Published
lastmod: 2026-04-25
---

# The Entity Framework Core ChangeTracker

The `ChangeTracker` is the EF Core component that tracks entity instances and their state inside the current `DbContext`.

It is one of the key parts behind normal EF Core saving behavior, because `SaveChangesAsync()` relies on tracked state to determine what should be inserted, updated, or deleted.

## ChangeTracker

In the standard EF Core workflow, the `ChangeTracker` tracks entities loaded or attached to the current `DbContext`.

A simple tracked update shows why it matters:

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

In this case, the entity is already tracked, so EF Core can detect the change and persist it when `SaveChangesAsync()` runs.

## Entity States

The `ChangeTracker` uses entity state to determine what `SaveChangesAsync()` should do later. For a deeper explanation of how EF Core tracks and transitions those states internally, see [Change Tracker: How It Works](/saving/change-tracker-how-it-works).

The most common states are:

* `Added` → usually `INSERT`
* `Modified` → usually `UPDATE`
* `Deleted` → usually `DELETE`
* `Unchanged` → usually no database command
* `Detached` → not tracked by the current context

## Tracking Queries vs No-Tracking Queries

By default, EF Core tracks entities returned by normal queries. That is why the earlier update example works without extra state-management code.

Now compare that with `AsNoTracking()`:

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .AsNoTracking()
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

Here, the entity is not tracked, so EF Core does not automatically persist the change.

This is one of the clearest examples of how tracking directly affects saving behavior.

For a broader explanation of how tracked entities behave during saving, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

## Common Tracking Scenarios

The following examples show common ways the `ChangeTracker` participates in normal EF Core workflows.

### Add a new entity

When you add a new entity through the current `DbContext`, EF Core begins tracking it as `Added`.

```csharp
using var context = new AppDbContext();

context.Products.Add(new Product
{
    Name = "Mechanical Keyboard",
    Price = 120
});

await context.SaveChangesAsync();
```

For more insertion patterns, including related entities and range methods, see [Adding Data](/saving/adding-data).

### Update a tracked entity

When you query an entity and modify it in the same context, EF Core tracks the change automatically.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

In a normal connected flow, calling `Update()` here is usually unnecessary because the entity is already tracked.

For broader update patterns, including disconnected and partial updates, see [Updating Data](/saving/modifying-data).

### Remove a tracked entity

When you remove an entity already tracked by the current `DbContext`, EF Core marks it as `Deleted`.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "27-inch Monitor");

context.Products.Remove(product);

await context.SaveChangesAsync();
```

For more delete patterns, including `RemoveRange`, see [Deleting Data](/saving/deleting-data).

### Call `Update()` on an already tracked entity

One common source of confusion is calling `Update()` even though EF Core is already tracking the entity.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

context.Update(product);
product.Price = 45;

await context.SaveChangesAsync();
```

## How the ChangeTracker Fits into a Normal Save Flow

In a normal connected flow, the `ChangeTracker` sits between the entity instance in memory and the database command that EF Core eventually generates.

A simplified sequence looks like this:

1. EF Core loads or attaches an entity
2. The `ChangeTracker` starts tracking that entity
3. One or more property values change
4. EF Core detects those changes
5. `SaveChangesAsync()` uses the tracked state to generate the appropriate SQL

To see the same workflow explained from the save pipeline side, see [SaveChanges](/saving/save-changes).

## Connected vs Not Tracked

The `ChangeTracker` is most useful when the entity remains tracked by the same `DbContext` from query time to save time.

If tracking is lost, the behavior changes.

For example, an entity may no longer be tracked when:

* the `DbContext` has already been disposed
* the entity came from another context instance
* the query used `AsNoTracking()`
* a DTO or payload was mapped into a new object outside the tracking flow
* the entity was explicitly detached

In those cases, EF Core no longer has the same tracked state to rely on.

This distinction matters because code can look similar on the surface even when the tracking behavior underneath is completely different.

For the normal tracked workflow where entities stay attached to the same context, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

## Why `Update()` Can Be Misleading

One of the most common mistakes in connected scenarios is calling `Update()` when the entity is already tracked.

If the entity was already loaded by the current `DbContext`, calling `Update()` usually adds no value and can make the code more misleading.

In many cases, it also hides the real lesson: the `ChangeTracker` was already doing the necessary work.

That is why a normal connected update is often simpler and clearer:

* query the entity
* modify it
* call `SaveChangesAsync()`

This becomes more important when you compare tracked updates with disconnected scenarios, where explicit state-setting patterns may actually be necessary.

## Common Pitfalls

Be careful when:

* using `AsNoTracking()` and still expecting automatic updates
* calling `Update()` even though the entity is already tracked
* assuming an entity is still tracked after the `DbContext` has been disposed
* keeping a `DbContext` alive too long and accumulating too many tracked entities
* confusing tracked entities with detached objects or DTOs
* mixing entities from different context instances

A useful rule of thumb is the following:

> If the entity was loaded and is still tracked by the same `DbContext`, EF Core usually does not need explicit attach or state-setting code.

## Performance Considerations

The `ChangeTracker` makes EF Core more convenient, but tracking has a cost.

Performance can be affected when:

* too many entities are tracked in the same context
* the context lives longer than necessary
* large graphs are loaded and kept alive unnecessarily
* `SaveChangesAsync()` is called repeatedly inside tight loops

Practical guidance:

* prefer short-lived contexts for normal units of work
* avoid tracking more data than you actually need
* use no-tracking queries for read-only work
* avoid long-lived contexts unless you have a specific reason
* batch work sensibly instead of saving one entity at a time

This does not make the `ChangeTracker` a problem by itself. It simply means tracking should be used deliberately.

## When to Rely on the Normal Tracking Flow

Use the normal tracking flow when:

* you query and modify entities within the same `DbContext`
* you want EF Core to manage state automatically
* you are working in a connected scenario
* you want the clearest and most idiomatic update path

Be careful or avoid wrong assumptions when:

* you are working with detached objects or DTOs
* you use `AsNoTracking()` and still expect automatic persistence
* you assume “same request” always means “same `DbContext`”
* you mix entity instances from different contexts
* you expect tracking to remain available after the context lifetime ends

## External Resources - ChangeTracker

The following resources provide deeper insight into how the `ChangeTracker` works in EF Core, how it interacts with `SaveChanges()`, and why tracking behavior is often the real reason behind unexpected updates or missing changes.

They are especially useful for understanding entity states, tracking scope per `DbContext`, why explicit `Update()` can be risky in tracked flows, and how tracking decisions can also affect performance in real applications.

### Video 1 — How does EF Core keep track of changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk?si=BH6TborwDYtcZV8K" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hubert Mijalski explains the `ChangeTracker` as the layer between your code and the database, showing how EF Core tracks changes through entity states and then uses that tracked state when `SaveChanges()` runs.

This is the strongest companion resource for this article because it also highlights one of the most important pitfalls: calling `Update()` unnecessarily can mark all properties as modified, generate broader SQL than needed, and increase concurrency risk.

**Key sections:**

* [00:00](https://youtu.be/uXDYEBexlYk?si=BH6TborwDYtcZV8K) — Introduction to the `ChangeTracker` and its role in `SaveChanges()`
* [01:00](https://youtu.be/uXDYEBexlYk?si=BH6TborwDYtcZV8K&t=60) — Entity states (`Added`, `Unchanged`, `Deleted`, `Detached`) with practical flows
* [03:00](https://youtu.be/uXDYEBexlYk?si=BH6TborwDYtcZV8K&t=180) — `AsNoTracking()` and why `Update()` exists for detached scenarios
* [06:00](https://youtu.be/uXDYEBexlYk?si=BH6TborwDYtcZV8K&t=360) — `Update()` pitfalls and SQL demo (changed property only vs full entity update)

### Video 2 — C# Entity Framework - More Advanced Topics on EF Core 5

<iframe width="560" height="315" src="https://www.youtube.com/embed/o9XoiPPP2Lw?si=uw6IJ1bHN8i16O_R" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Rainer Stropek provides a visual and debugger-driven explanation of the `ChangeTracker`, making it easier to see how entity states evolve during a normal EF Core workflow.

This is especially useful if you want a more in-depth look at how EF Core compares original values with current values, how states transition across the entity lifecycle, and how tracking is isolated per `DbContext`.

**Key sections:**

* [10:00](https://youtu.be/o9XoiPPP2Lw?si=uw6IJ1bHN8i16O_R&t=600) — Introduction to the `ChangeTracker` and first state transitions (`Detached` → `Added`)
* [20:00](https://youtu.be/o9XoiPPP2Lw?si=uw6IJ1bHN8i16O_R&t=1200) — Full state lifecycle with `SaveChanges()` (`Detached` → `Added` → `Unchanged` → `Modified` → `Deleted` → `Detached`)
* [33:20](https://youtu.be/o9XoiPPP2Lw?si=uw6IJ1bHN8i16O_R&t=2000) — `OriginalValues` and precise change detection for `UPDATE`
* [55:00](https://youtu.be/o9XoiPPP2Lw?si=uw6IJ1bHN8i16O_R&t=3300) — `AsNoTracking()`, tracking loss, and `DbContext`-scoped tracking behavior

### Video 3 — ChangeTracker e Performance con Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/J4x-bN3zSN4?si=MUEaOIS42xPJrp1L" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video adds an important performance angle by showing how the `ChangeTracker` can affect EF Core workloads when many entities are involved.

It is a valuable companion resource for understanding tracking overhead in inserts and reads, and for seeing practical optimization techniques such as `AddRange`, `AsNoTracking()`, and temporarily disabling `AutoDetectChangesEnabled` in high-volume scenarios.

**Key sections:**

* [01:30](https://youtu.be/J4x-bN3zSN4?si=MUEaOIS42xPJrp1L&t=90) — Entity and `DbContext` setup
* [04:00](https://youtu.be/J4x-bN3zSN4?si=MUEaOIS42xPJrp1L&t=240) — Benchmark: `Add` vs `AddRange` with 100k inserts
* [07:00](https://youtu.be/J4x-bN3zSN4?si=MUEaOIS42xPJrp1L&t=420) — Disabling `AutoDetectChangesEnabled` for better save performance
* [10:00](https://youtu.be/J4x-bN3zSN4?si=MUEaOIS42xPJrp1L&t=600) — Reads with `AsNoTracking()` and `QueryTrackingBehavior`

## Summary

The `ChangeTracker` is the EF Core component that keeps track of entity instances and their state inside a `DbContext`.

That tracked state is what allows `SaveChangesAsync()` to generate the right database commands during a normal connected save flow.

Understanding tracking boundaries, entity states, and when tracking is lost makes EF Core behavior much easier to predict.

## Related Articles

If you want to explore the most closely related saving topics, these pages are the best next step:

* [SaveChanges](/saving/save-changes) — broader overview of how EF Core persists tracked changes
* [Saving Data in Connected Scenario](/saving/save-changes-connected-entities) — how tracked entities behave in the normal connected save flow
* [Change Tracker: How It Works](/saving/change-tracker-how-it-works) — a deeper explanation of EF Core tracking internals

## FAQ

### What is the ChangeTracker in EF Core?

It is the EF Core component that keeps track of entity instances and their state within a `DbContext`.

### Does `SaveChanges()` use the ChangeTracker?

Yes. In normal tracked scenarios, `SaveChanges()` relies on the state information maintained by the `ChangeTracker`.

### What entity states does the ChangeTracker use?

The most common states are `Added`, `Modified`, `Deleted`, `Unchanged`, and `Detached`.

### Does `AsNoTracking()` disable the ChangeTracker?

For the entities returned by that query, yes in practice: EF Core does not track them, so later changes are not automatically persisted through the normal connected flow.

### Do I need to call `Update()` on a tracked entity?

Usually no. If the entity is already tracked by the current `DbContext`, calling `Update()` is often unnecessary.

### Why do some `SaveChanges()` issues turn out to be tracking issues?

Because `SaveChanges()` depends on tracked state. If the entity is not tracked, or not tracked by the current context, the behavior changes.
