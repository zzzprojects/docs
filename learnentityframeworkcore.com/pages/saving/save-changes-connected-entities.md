---
title: Saving Data in Connected Scenario in Entity Framework Core
description: Learn how saving data works in a connected scenario in Entity Framework Core, how tracked entities interact with SaveChanges, why explicit state setting is usually unnecessary, and the most common pitfalls to avoid.
canonical: /saving/save-changes-connected-entities
status: Published
lastmod: 2026-04-26
---

# Saving Data in Connected Scenario in Entity Framework Core

In EF Core, a connected scenario is the normal flow where entity changes are tracked and saved using the same `DbContext`.

The most common pattern is simple: load the entity, change its values, and call `SaveChangesAsync()`.

## Connected Scenario

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

In this case, the entity is already tracked by the current context, so EF Core can detect the change and persist it when `SaveChangesAsync()` runs.

For the broader saving pipeline, see [SaveChanges](/saving/save-changes).

## Insert a Related Entity

A connected scenario can also include inserting a new entity related to an entity that was loaded by the same context.

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

In this example, the category is queried first, and the new product is inserted using that category relationship.

For more insertion patterns, including range methods and related entities, see [Adding Data](/saving/adding-data).

## Update Multiple Properties

The same connected flow also works when more than one property changes on a tracked entity.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Name = "Ergonomic Wireless Mouse";
product.Price = 49;

await context.SaveChangesAsync();
```

Once the entity is tracked, EF Core can detect the modified properties and generate the required `UPDATE`.

For more update patterns, including disconnected updates and partial updates, see [Updating Data](/saving/modifying-data).

## Delete a Tracked Entity

To delete an entity in a connected scenario, query it first, mark it for removal, and then save the change.

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "27-inch Monitor");

context.Products.Remove(product);

await context.SaveChangesAsync();
```

In this case, the entity is already tracked, so `Remove()` marks it as deleted and `SaveChangesAsync()` sends the delete operation to the database.

For more delete patterns, including `RemoveRange`, see [Deleting Data](/saving/deleting-data).

## Mix Multiple Operations

A connected scenario can also combine several tracked changes before calling `SaveChangesAsync()`.

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

This works because all those changes are tracked by the same `DbContext` before `SaveChangesAsync()` runs.

## Common Connected Variations

Connected saving can appear in several common forms:

* update one tracked entity
* update multiple properties on a tracked entity
* add a new entity related to an existing entity
* remove a tracked entity
* save several tracked changes in one call

The examples above all follow the same rule: the entities being saved are already tracked, or are added to the same `DbContext`, before `SaveChangesAsync()` runs.

## How Connected Saving Works

In a connected scenario, EF Core already has the tracked state it needs when `SaveChangesAsync()` runs.

A simplified sequence looks like this:

1. EF Core loads or starts tracking an entity through the current `DbContext`
2. The entity remains tracked by that context
3. One or more changes are made in memory
4. EF Core detects or already knows about those pending changes
5. `SaveChangesAsync()` uses the tracked state to generate the appropriate SQL

This is why connected scenarios usually require very little state-management code.

For a broader explanation of the save pipeline, see [SaveChanges](/saving/save-changes). For a deeper look at tracking behavior, see [Change Tracker](/saving/change-tracker) and [Change Tracker: How It Works](/saving/change-tracker-how-it-works).

## Why `Attach()` and `Update()` Are Usually Not Needed

In a connected scenario, the entity is already being tracked by the current `DbContext`.

That means explicit state-management calls are usually unnecessary.

For example, this is normally not needed:

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

context.Update(product);

product.Price = 45;

await context.SaveChangesAsync();
```

The simpler version is usually clearer:

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

The second version better represents the connected workflow: EF Core is already tracking the entity, so changing the property and calling `SaveChangesAsync()` is enough.

The same idea applies to manual state configuration:

```csharp
context.Entry(product).State = EntityState.Modified;
```

That kind of explicit state setting is usually for cases where EF Core is not already tracking the entity.

Calling `Update()` is more commonly useful in disconnected scenarios, where the entity is not already tracked by the context doing the save.

## When a Scenario Stops Being Connected

A scenario is no longer connected when the entity is not tracked by the same `DbContext` that calls `SaveChangesAsync()`.

That can happen when:

* the `DbContext` has already been disposed
* the entity came from another context instance
* the entity was loaded using `AsNoTracking()`
* a DTO or payload was mapped into a new object
* the entity was explicitly detached

This distinction matters because the code may look similar even when the tracking behavior is completely different.

For example, this does not behave like a normal connected update:

```csharp
using var context = new AppDbContext();

var product = await context.Products
    .AsNoTracking()
    .SingleAsync(p => p.Name == "Wireless Mouse");

product.Price = 45;

await context.SaveChangesAsync();
```

In this case, the entity is not tracked, so `SaveChangesAsync()` does not automatically persist the change.

Use `AsNoTracking()` for read-only queries, not for entities you plan to modify and save through the same context.

## Connected vs Disconnected Scenario

The main difference is whether the entity is already tracked by the `DbContext` that performs the save.

In a connected scenario, EF Core already knows about the entity and can detect changes automatically.

In a disconnected scenario, the entity is not currently tracked by the saving context, so you often need to tell EF Core whether the object should be added, attached as existing, marked as modified, or ignored.

For update patterns beyond the connected workflow, see [Updating Data](/saving/modifying-data).

## Common Pitfalls

Connected scenarios are simple, but a few mistakes are common.

Be careful when:

* using `AsNoTracking()` and expecting automatic updates
* calling `Update()` on an entity already tracked by the current context
* assuming an entity is still tracked after the context has been disposed
* mixing entities from different context instances
* mapping a DTO into a new entity and expecting EF Core to know what changed
* keeping a `DbContext` alive too long and tracking too many entities

A useful rule of thumb is:

> If the entity was loaded and is still tracked by the same `DbContext`, you usually do not need `Attach()`, `Update()`, or manual state configuration.

## Performance Considerations

Connected scenarios are usually efficient for normal CRUD operations because EF Core only needs to save the tracked changes in the current unit of work.

However, performance can suffer when the connected flow is used for workloads that are too large for normal tracking, such as:

* loading thousands of entities only to update them one by one
* keeping the same `DbContext` alive across many operations
* calling `SaveChangesAsync()` repeatedly inside a loop
* loading and tracking large graphs when only a few values need to change

For normal CRUD, the connected pattern is usually the clearest option.

For set-based updates, set-based deletes, or high-volume persistence, consider [ExecuteUpdateAsync](/saving/execute-update), [ExecuteDeleteAsync](/saving/execute-delete), or specialized bulk operations designed for large write workloads.

## When to Use Connected Scenarios

Use a connected scenario when:

* you query and save within the same unit of work
* the entity remains tracked by the same `DbContext`
* you want EF Core to detect changes automatically
* you are performing standard CRUD operations
* you want simple, readable application code

This is often the best fit for ordinary create, update, and delete flows where the application works with entities directly.

## When Not to Rely on Connected Assumptions

Do not assume you are in a connected scenario when:

* the entity came from a different context
* the query used `AsNoTracking()`
* the original context has already been disposed
* the entity was reconstructed from a DTO or API payload
* you need to update or delete rows without loading entities first
* the operation is mainly set-based

In those cases, another pattern may be more appropriate.

The key distinction is simple: connected saving works because the entity is still tracked by the current `DbContext`.

## External Resources - Connected Scenario

The following resources help explain why connected scenarios are the most natural saving flow in EF Core, especially when entities remain tracked by the same `DbContext`.

They are especially useful for understanding how the `ChangeTracker` interacts with `SaveChanges()`, why explicit state setting is often unnecessary in connected updates, and what common mistakes can turn a simple tracked flow into an unexpected update problem.

### Video 1 - How does EF Core keep track of changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk?si=DGv0q_k1cAuO6uWR" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This is the strongest companion resource for understanding connected scenarios because it shows how EF Core tracks entity states internally and why `SaveChanges()` works naturally when the same `DbContext` continues tracking the entity.

It is especially useful for showing why explicit `Update()` calls are often unnecessary in connected flows and how they can even produce broader SQL updates than expected.

**Key sections:**

* [00:45](https://www.youtube.com/watch?v=uXDYEBexlYk&t=45s) — ChangeTracker introduction and basic states (`Added`, `Unchanged`)
* [02:30](https://www.youtube.com/watch?v=uXDYEBexlYk&t=150s) — `Remove()` flow: query → delete → `SaveChanges()`
* [04:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=240s) — Why `Update()` exists and why it becomes risky with detached or no-tracking flows
* [07:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=420s) — SQL demo: implicit tracked update vs explicit `Update()` (all properties vs changed properties)

### Video 2 - Part 21: Change Tracking in Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/caz8NIKncbM?si=X_8JrkFuCGmC6g9h" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hannah explains how EF Core tracks entity changes through the `ChangeTracker`, including the main entity states and how they affect what `SaveChanges()` does.

This is a useful support resource if you want a shorter, more step-by-step explanation of how tracked entities move through a normal connected save flow.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=caz8NIKncbM) — Intro to change tracking and the role of `DbContext` / `ChangeTracker`
* [00:45](https://www.youtube.com/watch?v=caz8NIKncbM&t=45s) — Entity states (`Added`, `Modified`, `Deleted`, `Unchanged`, `Detached`)
* [01:30](https://www.youtube.com/watch?v=caz8NIKncbM&t=90s) — Code example: query, modify, and inspect the `ChangeTracker`
* [02:30](https://www.youtube.com/watch?v=caz8NIKncbM&t=150s) — Summary of states and saving behavior

### Video 3 - Soft Deletes: The Upgrade Your EF Core Needs

<iframe width="560" height="315" src="https://www.youtube.com/embed/B9C4iK8IGbQ?si=PQCxfQnBPqr-vrn3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video is a more advanced companion resource, included here because it starts from a tracked delete operation and shows how the normal `SaveChanges` pipeline can be customized before SQL is generated.

It is useful for understanding that connected saving still goes through EF Core’s save pipeline, where behavior can be intercepted or reshaped before the final SQL command is sent.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=B9C4iK8IGbQ) — Problem statement: soft deletes vs hard deletes
* [01:00](https://www.youtube.com/watch?v=B9C4iK8IGbQ&t=60s) — Initial setup: model, `DbContext`, and in-memory database
* [03:00](https://www.youtube.com/watch?v=B9C4iK8IGbQ&t=180s) — `SaveChanges` interceptor for soft delete behavior
* [05:00](https://www.youtube.com/watch?v=B9C4iK8IGbQ&t=300s) — Query filters and `IgnoreQueryFilters()` for recovery scenarios

## Summary

A connected scenario in EF Core means that the same `DbContext` tracks the entity changes that will later be saved.

In practice, this is the standard flow for many CRUD operations:

* query and update a tracked entity
* add a new related entity
* remove a tracked entity
* combine several tracked changes in one `SaveChangesAsync()` call

The key point is that connected saving works because EF Core is already tracking the entities involved, or starts tracking them when they are added to the current context.

When tracking is lost through `AsNoTracking()`, a disposed context, another context instance, or a DTO-based flow, the scenario becomes disconnected and requires a different pattern.

## Related Articles

If you want to explore the most closely related saving topics, these pages are the best next step:

* [SaveChanges](/saving/save-changes) — broader overview of how EF Core persists tracked changes
* [Adding Data](/saving/adding-data) — insert patterns in normal tracked workflows
* [Updating Data](/saving/modifying-data) — tracked and disconnected update approaches
* [Deleting Data](/saving/deleting-data) — delete patterns with tracked entities
* [Change Tracker](/saving/change-tracker) — how EF Core tracks entity states and detects changes
* [Change Tracker: How It Works](/saving/change-tracker-how-it-works) — deeper explanation of EF Core tracking internals
* [ExecuteUpdate](/saving/execute-update) — set-based updates that run without loading or tracking entities
* [ExecuteDelete](/saving/execute-delete) — set-based deletes that run without loading or tracking entities

## FAQ

### What is a connected scenario in EF Core?

It is a scenario where entity changes are tracked and saved by the same `DbContext`, usually within the same unit of work.

### Do I need to call `Attach()` in a connected scenario?

Usually no. If the entity was loaded and is still tracked by the current context, `Attach()` is typically unnecessary.

### Do I need to call `Update()` in a connected scenario?

Usually no. If the entity is already tracked by the current context, calling `Update()` is often redundant and can be misleading.

### Why are my changes not saved after using `AsNoTracking()`?

Because the entity was not tracked when it was loaded, so EF Core does not automatically detect and persist those changes through the normal connected flow.

### Can a scenario stop being connected?

Yes. If the context is disposed, if the entity comes from another context, if tracking was disabled, or if the entity was detached, the scenario is no longer connected in the EF Core sense.

### Why does this matter for `SaveChangesAsync()`?

Because `SaveChangesAsync()` relies on tracked state. If the entity is not tracked by the current context, EF Core cannot treat it like a normal connected update.

### Is a connected scenario always the best choice?

No. It is often the simplest choice for normal CRUD work, but set-based updates, set-based deletes, large workloads, detached objects, and DTO-based flows may require different patterns.