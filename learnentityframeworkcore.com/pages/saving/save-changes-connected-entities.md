---
title: Saving Data in Connected Scenario in Entity Framework Core
description: Learn how saving data works in a connected scenario in Entity Framework Core, how tracked entities interact with SaveChanges, why explicit state setting is usually unnecessary, and the most common pitfalls to avoid.
canonical: /saving/deleting-data
status: Published
lastmod: 2026-04-07
---

# Saving Data in Connected Scenario in Entity Framework Core

In a connected scenario, an entity is loaded, modified, and saved while it remains tracked by the same `DbContext` instance.

This is the most natural saving flow in EF Core, because `SaveChanges()` works directly with the changes already tracked by the context.

This page focuses on what makes connected scenarios predictable, how the `ChangeTracker` is involved, when `SaveChanges()` works without extra state configuration, and where common mistakes still happen.

Related pages:
- SaveChanges → /savechanges
- ChangeTracker → /changetracker
- Tracking Changes of Entities in EF Core → /tracking-changes-of-entities-in-ef-core

## What Is a Connected Scenario in EF Core

A connected scenario happens when:

- An entity is queried by a `DbContext`
- That same `DbContext` continues tracking the entity
- You modify the tracked entity
- You call `SaveChanges()` using that same context

The key idea is not simply that the entity came from the database earlier. 

The important part is that the entity is still being tracked when `SaveChanges()` is called.

That is what makes the scenario “connected”.

## Why Connected Scenarios Are Usually Simpler

Connected scenarios are usually the easiest way to persist updates in EF Core because the framework already has the information it needs.

When the entity remains tracked:

- EF Core knows the entity is attached to the current context
- EF Core can detect which properties changed
- `SaveChanges()` can generate the required SQL automatically
- You usually do not need `Attach()`
- You usually do not need `Update()`
- You usually do not need to set `EntityState.Modified`

This is why connected scenarios often feel more predictable than disconnected ones.

## Basic Flow: Query → Modify → Save

A typical connected scenario looks like this:

```csharp
using var db = new AppDbContext();

var product = db.Products.First();
product.Price += 10;

db.SaveChanges();
````

At a high level, EF Core does the following:

1. Loads the entity and begins tracking it
2. Detects the property change
3. Marks the entity as modified internally
4. Generates the corresponding `UPDATE` statement
5. Executes the command when `SaveChanges()` is called

In this flow, no explicit attach logic is needed because the entity is already tracked by the current `DbContext`.

## The Role of the ChangeTracker

Connected scenarios depend heavily on the `ChangeTracker`.

When EF Core materializes an entity through a tracking query, it keeps enough information to later determine what changed.

That tracked state is what allows `SaveChanges()` to work without asking you to manually specify every operation.

In practice, `SaveChanges()` relies on the `ChangeTracker` as its source of truth.

That is why connected scenarios are strongly tied to:

* how the entity was queried
* whether tracking is enabled
* whether the same `DbContext` instance is still alive
* whether the entity is still attached when saving happens

If tracking is lost, the scenario is no longer truly connected.

## What SaveChanges Does in a Connected Scenario

In a connected scenario, `SaveChanges()` does not need you to tell it “this entity should be updated” in the normal case.

It already knows the entity is tracked and can infer the correct operation from the tracked state.

For example:

* New tracked entity added with `Add()` → `INSERT`
* Existing tracked entity modified → `UPDATE`
* Tracked entity removed with `Remove()` → `DELETE`

That makes connected scenarios the clearest place to understand the normal EF Core saving model.

## Example: Insert in a Connected Flow

```csharp
using var db = new AppDbContext();

var product = new Product
{
    Name = "Keyboard",
    Price = 120
};

db.Products.Add(product);
db.SaveChanges();
```

In this case:

* The entity becomes tracked as `Added`
* `SaveChanges()` generates an `INSERT`
* Generated values (such as identity keys) may be propagated back after success

## Example: Update in a Connected Flow

```csharp
using var db = new AppDbContext();

var product = db.Products.First(p => p.Id == 10);
product.Price = 150;

db.SaveChanges();
```

In this case:

* The entity is queried and tracked by the current context
* EF Core detects the property change
* `SaveChanges()` generates the required `UPDATE`

This is the classic connected update pattern.

## Example: Delete in a Connected Flow

```csharp
using var db = new AppDbContext();

var product = db.Products.First(p => p.Id == 10);

db.Products.Remove(product);
db.SaveChanges();
```

In this case:

* The entity is tracked
* `Remove()` marks it for deletion
* `SaveChanges()` generates the corresponding `DELETE`

## Why Attach() and Update() Are Usually Not Needed Here

One of the most common mistakes in connected scenarios is adding explicit state-management code when EF Core is already tracking the entity.

For example, this is often unnecessary in a connected scenario:

```csharp
db.Update(product);
```

If `product` was already loaded by the current `DbContext`, calling `Update()` usually adds no value and can make the code more misleading.

The same applies to patterns like:

```csharp
db.Attach(product);
db.Entry(product).State = EntityState.Modified;
```

Those patterns are typically associated with disconnected scenarios, not the normal connected flow.

In a real connected scenario, the simplest code is often the most correct:

* query the entity
* modify it
* call `SaveChanges()`

## When a Scenario Stops Being Connected

A scenario is no longer truly connected when the entity is no longer tracked by the same `DbContext`.

That can happen when:

* the `DbContext` was disposed
* the entity came from another context instance
* the entity was materialized using `AsNoTracking()`
* a DTO or payload was mapped into a new object outside the tracking flow

This distinction matters because many bugs happen when code assumes an entity is still tracked when it is not.

## AsNoTracking and Connected Saving

`AsNoTracking()` is useful for read-only queries, but it changes the saving story.

For example:

```csharp
using var db = new AppDbContext();

var product = db.Products
    .AsNoTracking()
    .First(p => p.Id == 10);

product.Price = 150;

db.SaveChanges();
```

In this example, `SaveChanges()` does not persist the change automatically because the entity was not tracked when it was loaded.

This is one of the clearest examples of why connected scenarios depend on tracking—not just on querying data earlier.

## Common Pitfalls

Connected scenarios are simpler, but they are not immune to mistakes.

Be careful when:

* Using `AsNoTracking()` and expecting automatic updates
* Calling `Update()` on entities that are already tracked
* Assuming an entity is still tracked after the context has been disposed
* Keeping a `DbContext` alive too long and accumulating too many tracked entities
* Confusing tracked entities with DTOs or detached objects
* Mixing entities from different context instances

A useful rule of thumb is:

> If the entity was loaded and is still tracked by the same `DbContext`, you usually do not need explicit attach/state-setting code.

## Connected vs Disconnected Scenario

The difference between connected and disconnected scenarios is one of the most important concepts in EF Core saving behavior.

### Connected scenario

* Entity is tracked by the same `DbContext`
* Changes are usually detected automatically
* `SaveChanges()` works with minimal explicit state management

### Disconnected scenario

* Entity is not currently tracked by the context doing the save
* You often need `Attach()`, `Update()`, or explicit state configuration
* The risk of overposting and unintended updates is higher

This is why many “SaveChanges problems” are actually tracking problems.

## Performance Considerations

Connected scenarios are convenient, but tracking has a cost.

Performance can be affected when:

* too many entities are tracked in the same context
* the context lives longer than necessary
* `SaveChanges()` is called repeatedly inside tight loops
* large graphs are loaded and kept alive unnecessarily

Practical guidance:

* Prefer short-lived contexts for normal units of work
* Avoid tracking large amounts of data longer than necessary
* Batch changes sensibly instead of saving one entity at a time
* Use no-tracking queries for read-only scenarios, but only when you truly do not intend to save those entities through the same context

## When to Use vs When NOT to Use Connected Scenarios

Use connected scenarios when:

* You query and modify entities within the same `DbContext`
* You want EF Core to detect changes automatically
* You want the simplest and most idiomatic `SaveChanges()` flow
* You are handling standard CRUD within a normal unit of work

Be careful / avoid wrong assumptions when:

* You use `AsNoTracking()` and still expect automatic updates
* You call `Update()` even though the entity is already tracked
* You assume “same request” always means “same `DbContext`”
* You mix tracked entities, DTOs, or objects coming from different context instances
* You keep a `DbContext` alive too long and accumulate too many tracked entities

Connected scenarios are often the best fit for ordinary CRUD work, especially when querying and saving happen within the same logical unit of work.

## External Resources — Connected Scenario

The following resources help explain why connected scenarios are the most natural saving flow in EF Core, especially when entities remain tracked by the same `DbContext`.

They are especially useful for understanding how the `ChangeTracker` interacts with `SaveChanges()`, why explicit state setting is often unnecessary in connected updates, and what common mistakes can turn a simple tracked flow into an unexpected update problem.

### Video 1 — [How does EF Core keep track of changes?](https://www.youtube.com/watch?v=uXDYEBexlYk)

This is the strongest companion resource for understanding connected scenarios because it shows how EF Core tracks entity states internally and why `SaveChanges()` works naturally when the same `DbContext` continues tracking the entity.
It is especially useful for showing why explicit `Update()` calls are often unnecessary in connected flows and how they can even produce broader SQL updates than expected.

Key sections:

* 00:45 — ChangeTracker introduction and basic states (`Added`, `Unchanged`)
* 02:30 — `Remove()` flow: query → delete → `SaveChanges()`
* 04:00 — Why `Update()` exists and why it becomes risky with detached or no-tracking flows
* 07:00 — SQL demo: implicit tracked update vs explicit `Update()` (all properties vs changed properties)

### Video 2 — [Part 21: Change Tracking in Entity Framework Core](https://www.youtube.com/watch?v=caz8NIKncbM)

Hannah explains how EF Core tracks entity changes through the `ChangeTracker`, including the main entity states and how they affect what `SaveChanges()` does.
This is a useful support resource if you want a shorter, more step-by-step explanation of how tracked entities move through a normal connected save flow.

Key sections:

* 00:00 — Intro to change tracking and the role of `DbContext` / `ChangeTracker`
* 00:45 — Entity states (`Added`, `Modified`, `Deleted`, `Unchanged`, `Detached`)
* 01:30 — Code example: query, modify, and inspect the `ChangeTracker`
* 02:30 — Summary of states and saving behavior

### Video 3 — [Soft Deletes: The Upgrade Your EF Core Needs](https://www.youtube.com/watch?v=B9C4iK8IGbQ)

This video is a more advanced companion resource that shows how the normal `SaveChanges()` pipeline can be customized in EF Core.
It is useful here because it demonstrates that even when a delete starts as a tracked entity operation, EF Core behavior can be intercepted and reshaped before SQL is generated.

Key sections:

* 00:00 — Problem statement: soft deletes vs hard deletes
* 01:00 — Initial setup: model, `DbContext`, and in-memory database
* 03:00 — `SaveChanges` interceptor for soft delete behavior
* 05:00 — Query filters and `IgnoreQueryFilters()` for recovery scenarios

## Summary & Next Steps

A connected scenario in EF Core means the entity remains tracked by the same `DbContext` from query time to save time.

That is what allows `SaveChanges()` to persist changes with minimal explicit state management.

The more clearly you understand tracking boundaries, the easier it becomes to know when EF Core will save changes automatically—and when it will not.

Next steps:

* SaveChanges → /savechanges
* ChangeTracker → /changetracker
* Tracking Changes of Entities in EF Core → /tracking-changes-of-entities-in-ef-core

## FAQ

**What is a connected scenario in EF Core?**
It is a scenario where the entity is loaded, modified, and saved while still being tracked by the same `DbContext`.

**Do I need to call Attach() in a connected scenario?**
Usually no. If the entity was loaded and is still tracked by the current `DbContext`, `Attach()` is typically unnecessary.

**Do I need to call Update() in a connected scenario?**
Usually no. If the entity is already tracked, calling `Update()` is often redundant and can be misleading.

**Why are my changes not saved after using AsNoTracking()?**
Because the entity was not tracked when it was loaded, so EF Core does not automatically detect and persist those changes.

**Can a scenario stop being connected?**
Yes. If the context is disposed, if the entity comes from another context, or if tracking was disabled, the scenario is no longer connected in the EF Core sense.

**Why does this matter for SaveChanges()?**
Because `SaveChanges()` relies on tracked state. If the entity is not tracked, EF Core cannot treat it like a normal connected update.

```