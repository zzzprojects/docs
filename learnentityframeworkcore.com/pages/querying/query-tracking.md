---
title: Query Tracking in EF Core
description: Learn when to use tracking, AsNoTracking, AsNoTrackingWithIdentityResolution, and QueryTrackingBehavior in Entity Framework Core.
canonical: /querying/query-tracking
status: Published
lastmod: 2026-08-18
---

# Query Tracking in EF Core

Entity Framework Core tracks entities returned by queries by default so changes to those entities can be detected and saved. Use no-tracking queries when the current `DbContext` does not need to track the returned entities for later updates.

## Query with Tracking

Queries that return entity types with a key are tracking by default.

The following query returns products as tracked entities:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

After `ToListAsync()`, the result is a `List<Product>`, and the returned `Product` instances are registered in the current `DbContext`'s [ChangeTracker](/saving/change-tracker).

Because the entities are tracked, EF Core can detect changes made to them and persist those changes later with `SaveChanges()` or `SaveChangesAsync()`.

If an entity with the same key is already being tracked by the context, EF Core returns the existing tracked instance instead of materializing another instance for that entity.

## Query with `AsNoTracking()`

Use `AsNoTracking()` when the returned entities do not need to be tracked by the current `DbContext`.

```csharp
var products = await context.Products
    .AsNoTracking()
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

The result is still a `List<Product>`, but the returned `Product` instances are not added to the context's change tracker.

`AsNoTracking()` does not make the objects immutable. The application can still change their property values in memory, but those changes are not automatically tracked by this `DbContext` for persistence through `SaveChanges()`.

Like other EF Core query operators, `AsNoTracking()` configures the query without executing it. `ToListAsync()` executes the query and materializes the `Product` objects. See [LINQ Queries](/querying/linq-queries) for more about query composition and execution.

## Query with `AsNoTrackingWithIdentityResolution()`

Use `AsNoTrackingWithIdentityResolution()` when the query should not track entities in the current `DbContext`, but repeated occurrences of the same entity should reuse the same instance.

The difference becomes important when the same entity can appear more than once in a result. Suppose two products belong to the same category. The following query returns each product's category:

```csharp
var categories = await context.Products
    .OrderBy(product => product.ProductId)
    .Select(product => product.Category)
    .AsNoTrackingWithIdentityResolution()
    .ToListAsync();
```

After `ToListAsync()`, the result is a `List<Category>`.

If multiple results represent the same category, EF Core reuses the same `Category` instance for those occurrences. The returned categories are still not tracked by the current `DbContext`.

EF Core performs this identity resolution with a separate change tracker used only while the query results are materialized.

By comparison, `AsNoTracking()` does not perform identity resolution, so each occurrence of the same database entity is materialized as a separate instance.

## Choose a Tracking Behavior

Use the tracking behavior that matches what the application needs to do with the returned entities.

| Query behavior                         | Tracked by `DbContext` | Identity resolution | Typical use                                            |
| -------------------------------------- | ---------------------- | ------------------- | ------------------------------------------------------ |
| Tracking                               | Yes                    | Yes                 | Modify and save entities through the same context      |
| `AsNoTracking()`                       | No                     | No                  | Read entities without tracking them for later updates  |
| `AsNoTrackingWithIdentityResolution()` | No                     | Yes                 | Avoid tracking while reusing repeated entity instances |

Tracking is the default behavior for queries that return entity types with a key.

## Configure the Default with `QueryTrackingBehavior`

If many queries should use the same tracking behavior, configure that behavior as the default for the context.

The following example makes no-tracking the default for the current context:

```csharp
context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

var products = await context.Products
    .ToListAsync();
```

With this configuration, the query is no-tracking even without calling `AsNoTracking()`.

`QueryTrackingBehavior` supports `TrackAll`, `NoTracking`, and `NoTrackingWithIdentityResolution`.

### Override the Default with `AsTracking()`

Use `AsTracking()` when a specific query should be tracking even though the context defaults to no-tracking.

```csharp
context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

var products = await context.Products
    .AsTracking()
    .ToListAsync();
```

`AsTracking()` overrides the no-tracking default for this query.

## Query Tracking and Projection

Projection does not automatically disable tracking. If a projected result contains an entity instance, that entity is tracked by default in a tracking query.

```csharp
var results = await context.Products
    .Select(product => new
    {
        Product = product,
        product.Name
    })
    .ToListAsync();
```

The anonymous result itself is not an entity, but the `Product` instance inside it is tracked.

A projection containing only scalar values does not contain entity instances to track. See [Projection](/querying/projection) for more ways to shape query results with [`Select`](/querying/linq-methods#select).

## External Resources - Query Tracking

The following videos provide practical demonstrations of query tracking behavior in EF Core. The first focuses on identity resolution and the difference between `AsNoTracking()` and `AsNoTrackingWithIdentityResolution()`, while the second covers a broader set of tracking APIs, including `AsTracking()` and `UseQueryTrackingBehavior`.

### Video 1 - EF Core - Usando AsNoTrackingWithIdentityResolution

<iframe width="560" height="315" src="https://www.youtube.com/embed/IHwAa3eVSe0" title="EF Core - Usando AsNoTrackingWithIdentityResolution" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Jose Carlos Macoratti provides a clear visual demonstration of the difference between tracking, `AsNoTracking()`, and `AsNoTrackingWithIdentityResolution()`. Using related `Project` and `Client` entities, he shows how repeated references to the same client behave as separate instances with `AsNoTracking()` and reuse the same instance when identity resolution is enabled.

**Key timestamps:**

* [6:28](https://www.youtube.com/watch?v=IHwAa3eVSe0&t=388s) — Tracking query and automatic identity resolution for repeated `Client` references
* [9:31](https://www.youtube.com/watch?v=IHwAa3eVSe0&t=571s) — `AsNoTracking()` disables tracking and identity resolution
* [15:18](https://www.youtube.com/watch?v=IHwAa3eVSe0&t=918s) — `AsNoTrackingWithIdentityResolution()` and the temporary identity map
* [16:41](https://www.youtube.com/watch?v=IHwAa3eVSe0&t=1001s) — Demonstration that repeated `Client` references reuse the same instance

The video focuses specifically on identity resolution, making it a useful companion to the broader tracking options covered in this article.

### Video 2 - Entity Framework Core #20 - AsNoTracking, AsNoTrackingWithIdentityResolution, UseQueryTrackingBehavior

<iframe width="560" height="315" src="https://www.youtube.com/embed/O4SiALqmqMY" title="Entity Framework Core #20 - AsNoTracking, AsNoTrackingWithIdentityResolution, UseQueryTrackingBehavior" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Gençay Yıldız covers a broader set of query tracking APIs, including `AsNoTracking()`, `AsNoTrackingWithIdentityResolution()`, `AsTracking()`, and `UseQueryTrackingBehavior`. The video complements the previous resource by showing both per-query tracking operators and configuration of the default tracking behavior.

**Key timestamps:**

* [0:46](https://www.youtube.com/watch?v=O4SiALqmqMY&t=46s) — `AsNoTracking()` and disabling tracking for a query
* [16:14](https://www.youtube.com/watch?v=O4SiALqmqMY&t=974s) — `AsNoTrackingWithIdentityResolution()` and identity resolution without normal context tracking
* [35:20](https://www.youtube.com/watch?v=O4SiALqmqMY&t=2120s) — `AsTracking()` and explicitly enabling tracking for a query
* [39:27](https://www.youtube.com/watch?v=O4SiALqmqMY&t=2367s) — `UseQueryTrackingBehavior` and configuring the default tracking behavior

The code demonstrations and API sections are clearly visible, and all four timestamps were manually verified.

## Summary

EF Core tracks entity queries by default, but each query can use a different tracking behavior when needed.

* Use the default tracking behavior when returned entities may be modified and saved through the same `DbContext`.
* Use `AsNoTracking()` when the context does not need to track the returned entities.
* Use `AsNoTrackingWithIdentityResolution()` when tracking is not needed but repeated occurrences of the same entity should reuse the same instance.
* Use `QueryTrackingBehavior` to configure the default behavior, and query-level operators such as `AsTracking()` to override it when needed.

## Related Articles

* [LINQ Queries](/querying/linq-queries) — Learn how to build, compose, and execute EF Core queries.
* [Projection](/querying/projection) — Learn how projected results interact with tracking when they contain entity instances.
* [ChangeTracker](/saving/change-tracker) — Learn how EF Core keeps track of entity instances and their state inside a `DbContext`.

## FAQ

### Does `AsNoTracking()` make entities read-only?

No. The returned objects can still be modified in memory. `AsNoTracking()` means the current `DbContext` does not track those instances for automatic change detection and persistence through `SaveChanges()`.

### Is `AsNoTracking()` always better for read queries?

No. No-tracking queries avoid the work required to set up normal change tracking, but tracking queries perform identity resolution and can reuse existing tracked instances. Choose the behavior based on whether the context needs to track the entities and whether identity resolution is useful for the result.

### Can I override `QueryTrackingBehavior` for a specific query?

Yes. Query-level methods can override the configured default. For example, `AsTracking()` makes a specific query tracking even when the context uses `QueryTrackingBehavior.NoTracking` by default.
