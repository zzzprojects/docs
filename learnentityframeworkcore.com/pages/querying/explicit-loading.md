---
title: Explicit Loading in EF Core
description: Learn how to explicitly load reference and collection navigations in EF Core, query related data with Query(), and use relationship fixup.
canonical: /querying/explicit-loading
status: Published
lastmod: 2026-08-28
---

# Explicit Loading in EF Core

Explicit loading lets you load related data **on demand, after the main entity has already been retrieved**. In EF Core, explicit loading starts from `DbContext.Entry(...)`.

If you already know the related data is needed with the initial query, see [Eager Loading in EF Core](/querying/eager-loading). Explicit loading is useful when that decision should happen later.

## Load a Reference Navigation Explicitly

Use `Reference(...)` when the navigation points to a **single related entity**.

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

await context.Entry(blog)
    .Reference(blog => blog.Owner)
    .LoadAsync();

Console.WriteLine(blog.Owner?.Name);
```

The first query retrieves the `Blog` without explicitly requesting its `Owner` navigation.

`context.Entry(blog)` accesses the EF Core entry for the loaded `Blog`. `Reference(blog => blog.Owner)` selects the `Owner` navigation, and `LoadAsync()` loads the related entity from the database.

Once the load completes, the related entity is available through `blog.Owner`.

Calling `Entry(...)` and `Reference(...)` only identifies the entity and navigation to work with. The related data is not explicitly loaded until `LoadAsync()` is called.

This is different from [Eager Loading in EF Core](/querying/eager-loading), where related data is requested as part of the initial query. Explicit loading lets the application decide later whether and when the navigation should be loaded.

For navigations that contain multiple related entities, use `Collection(...)` instead.

## Load a Collection Navigation Explicitly

Use `Collection(...)` when the navigation contains **multiple related entities**.

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

await context.Entry(blog)
    .Collection(blog => blog.Posts)
    .LoadAsync();

foreach (var post in blog.Posts)
{
    Console.WriteLine(post.Title);
}
```

The first query retrieves the `Blog` without explicitly requesting its `Posts` navigation.

`Collection(blog => blog.Posts)` selects the collection navigation, and `LoadAsync()` loads the related `Post` entities. After the operation completes, the posts are available through `blog.Posts`.

The distinction between the two APIs is the shape of the navigation:

* `Reference(...)` targets a single related entity, such as `blog.Owner`.
* `Collection(...)` targets multiple related entities, such as `blog.Posts`.

Both follow the same explicit-loading pattern:

```csharp
await context.Entry(entity)
    .Reference(...)
    .LoadAsync();

await context.Entry(entity)
    .Collection(...)
    .LoadAsync();
```

If you do not need the entire collection—for example, if you only need a count or a filtered subset—you can access the navigation as a query with `Query()`.

## Query a Navigation with `Query()`

`Query()` exposes the query that represents a navigation, allowing you to compose LINQ operators before executing it.

### Get Information Without Loading the Entire Collection

For example, you can count the related posts without loading them into memory:

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

var postCount = await context.Entry(blog)
    .Collection(blog => blog.Posts)
    .Query()
    .CountAsync();

Console.WriteLine(postCount);
```

`Collection(blog => blog.Posts)` identifies the `Posts` navigation, while `Query()` exposes it as a LINQ query.

`CountAsync()` executes an aggregate query and returns the number of related posts as an `int`. The application gets the count without materializing the `Posts` collection.

### Filter Related Entities

You can also compose the navigation query before materializing matching entities.

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

var goodPosts = await context.Entry(blog)
    .Collection(blog => blog.Posts)
    .Query()
    .Where(post => post.Rating > 3)
    .ToListAsync();

foreach (var post in goodPosts)
{
    Console.WriteLine(post.Title);
}
```

Here, `Query()` exposes the related `Posts` as a query, `Where(...)` filters it, and `ToListAsync()` executes the query and returns a `List<Post>` containing only the matching entities.

In a tracking query, those `Post` entities can also be added to `blog.Posts` through relationship fixup. However, querying only part of the collection with `Query()` does not mark the navigation as fully loaded.

`Query()` itself does not execute the database query. It returns an `IQueryable` that can be composed with LINQ operators. Execution happens when a terminal operation such as `CountAsync()` or `ToListAsync()` is called.

Use `Query()` when you need to query a navigation rather than load all of its related entities with `LoadAsync()`.

## Load Related Entities Through the Change Tracker

Related entities can also become connected through EF Core's Change Tracker when they are loaded by a separate query.

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

var posts = await context.Posts
    .Where(post => post.BlogId == blog.BlogId)
    .ToListAsync();
```

The first query loads the `Blog`. The second query separately materializes its related `Post` entities.

When change tracking is enabled, EF Core recognizes relationships between newly materialized entities and entities that are already being tracked. It then performs relationship fixup by updating the relevant navigation properties.

In this example, EF Core connects the newly loaded `Post` entities to the already tracked `Blog`.

This is different from calling `Collection(...).LoadAsync()` or `Reference(...).LoadAsync()`. Those APIs explicitly target a navigation to load. Here, the application runs a separate query, and the Change Tracker connects the related tracked entities.

For a deeper explanation of tracking behavior, see [Query Tracking in EF Core](/querying/query-tracking).

## Explicit Loading vs. Eager Loading vs. Lazy Loading

EF Core supports different strategies for loading related data. The main difference is **when** the related data is loaded.

| Loading strategy | When related data is loaded                                             |
| ---------------- | ----------------------------------------------------------------------- |
| Eager loading    | As part of the initial query                                            |
| Explicit loading | After the main entity has been loaded, when the application requests it |
| Lazy loading     | Automatically when a navigation property is accessed                    |

Use [Eager Loading in EF Core](/querying/eager-loading) when you already know the related data is needed with the main query.

Use explicit loading when related data should be retrieved later, under application control.

Lazy loading follows a different model because EF Core loads related data automatically when a navigation is accessed. See **Lazy Loading in EF Core** *(Coming soon)* for that behavior.

## When Should You Use Explicit Loading?

Use explicit loading when you already have the main entity and whether related data is needed depends on what happens next.

Typical cases include:

* loading a navigation only when a condition is met;
* retrieving only specific navigations after the main entity has already been loaded;
* querying a relationship with `Query()` instead of loading the entire navigation.

Because explicit loading can execute additional database queries, avoid repeatedly loading navigations without considering how many queries the application will generate.

## External Resources - Explicit Loading

The following videos complement the examples in this article with practical demonstrations of explicit loading in EF Core. Together, they cover collection and reference navigations, conditional and nested loading with `LoadAsync()`, relationship fixup through the Change Tracker, and navigation queries built with `Query()`.

### Video 1 - Eager, Lazy and Explicit Loading in Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/T9fTFynqvCw" title="Eager, Lazy and Explicit Loading in Entity Framework Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

DotNetMastery demonstrates explicit loading with both collection and reference navigations using `DbContext.Entry(...)`, `Collection(...)`, `Reference(...)`, and `Load()`. The debugger and EF Core logs make it possible to see when related data is not initially populated and when an additional database query is executed to load it.

The video also shows a useful Change Tracker behavior: a `VillaAmenity.Villa` reference is already populated because the related `Villa` was previously loaded in the same `DbContext`. Although the presenter does not use the formal term *relationship fixup*, the demonstration provides a clear visual example of that behavior.

The timestamps below focus on the Explicit Loading segments that remain directly relevant to current EF Core usage.

**Key timestamps:**

* [19:08](https://www.youtube.com/watch?v=T9fTFynqvCw&t=1148s) — Introduces explicit loading and explains that related data can be loaded later as either a collection or a reference navigation.
* [20:27](https://www.youtube.com/watch?v=T9fTFynqvCw&t=1227s) — Builds the collection example with `Entry(...).Collection(...).Load()` to load `VillaAmenity` for a single `Villa`.
* [21:50](https://www.youtube.com/watch?v=T9fTFynqvCw&t=1310s) — Introduces explicit loading for a reference navigation and explains why `Reference(...)` is used instead of `Collection(...)`.
* [23:06](https://www.youtube.com/watch?v=T9fTFynqvCw&t=1386s) — Shows the related `Villa` already populated because it was previously loaded in the same `DbContext`, providing a practical illustration of relationship fixup.

### Video 2 - Eager, Explicit & Lazy Loading in EF Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/LSL3Bgf_nno" title="Eager, Explicit & Lazy Loading in EF Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz Zalewski demonstrates explicit loading with the asynchronous `LoadAsync()` API in a practical e-commerce scenario. Instead of loading every relationship up front, the example applies business rules to decide which customers, orders, items, and products should have related data loaded.

It shows `Collection(...).LoadAsync()` for collection navigations and `Reference(...).LoadAsync()` for a single related entity, then executes the flow while displaying the additional SQL statements generated by those explicit loads.

**Key timestamps:**

* [11:35](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=695s) — Introduces the business rule that determines which customers should have related data loaded explicitly instead of loading every relationship up front.
* [12:34](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=754s) — Uses `Entry(customer).Collection(...).LoadAsync()` to load the customer's `Orders` collection asynchronously.
* [14:29](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=869s) — Uses `Entry(item).Reference(...).LoadAsync()` to load a related `Product` only when the item's quantity satisfies the configured condition.
* [15:40](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=940s) — Executes the explicit-loading flow and shows the additional SQL statements produced according to the business rules.

This resource adds useful depth by showing how explicit loading can be applied asynchronously and selectively across multiple levels of a related entity graph.

### Video 3 - ASP.NET Web API + Entity Framework Core: Loading Related Data | Explicit Loading - EP06

<iframe width="560" height="315" src="https://www.youtube.com/embed/gjBn5f23Y1k" title="ASP.NET Web API + Entity Framework Core: Loading Related Data | Explicit Loading - EP06" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

CuriousDrive demonstrates how `Query()` can be used to compose a query over a navigation before execution. This complements the previous resources, which focus primarily on `Load()` and `LoadAsync()`.

The useful segments show two practical navigation-query patterns: adding `Include(...)` to load another related level and applying `Where(...)` to filter which related entities are loaded. Because the video predates filtered `Include`, the timestamps below focus specifically on its still-useful `Query()` demonstrations.

**Key timestamps:**

* [5:11](https://www.youtube.com/watch?v=gjBn5f23Y1k&t=311s) — Uses `Collection(...).Query()` on the publisher's `Books` navigation, exposing the related books as a composable query.
* [5:56](https://www.youtube.com/watch?v=gjBn5f23Y1k&t=356s) — Shows the complete `Collection(...).Query().Include(...).Load()` pattern to load books together with their related `Sales`.
* [6:21](https://www.youtube.com/watch?v=gjBn5f23Y1k&t=381s) — Builds a filtered navigation query with `Collection(...).Query().Where(...)` to restrict which related users are loaded.
* [6:49](https://www.youtube.com/watch?v=gjBn5f23Y1k&t=409s) — Shows the filtered result, where the `Users` navigation contains only the entity matching the configured condition.

This resource adds the `Query()` dimension to the set and shows how explicit loading can be combined with LINQ composition before related entities are materialized.

## Summary

Explicit loading gives you control over when related data is retrieved after the main entity has already been loaded.

* Use `Reference(...).LoadAsync()` for a single related entity and `Collection(...).LoadAsync()` for a collection.
* Use `Query()` to count, filter, or otherwise compose a query over a navigation before execution.
* With change tracking enabled, separately loaded related entities can also be connected through relationship fixup.

## Related Articles

Explicit loading is one of several ways EF Core can work with related data. The following articles explain the neighboring querying and loading behaviors in more detail:

* [Eager Loading in EF Core](/querying/eager-loading) — Load related data together with the main entity.
* **Lazy Loading in EF Core** *(Coming soon)* — Load related data automatically when a navigation is accessed.
* [Include in EF Core](/querying/include) — Use `Include` and `ThenInclude` to specify related data in the original query.
* [Query Tracking in EF Core](/querying/query-tracking) — Understand how tracked and no-tracking queries behave.
* [LINQ Queries in EF Core](/querying/linq-queries) — Learn how EF Core queries are composed and executed.

## FAQ

### Does explicit loading always load the entire navigation?

No. `LoadAsync()` loads the targeted navigation, but `Query()` lets you work with its underlying query instead. You can apply operators such as `CountAsync()` or `Where(...)` when you do not need the entire relationship.

### Does explicit loading execute another database query?

Calling `LoadAsync()` for an unloaded navigation normally executes a separate database query after the query that loaded the main entity. Once the navigation is marked as loaded, calling `LoadAsync()` again does not execute another query.

### Does `Query()` mark a navigation as loaded?

Not necessarily. For example, querying only part of a collection with `Query().Where(...)` does not mark the collection navigation as fully loaded.

### Can entities returned through `Query()` update the navigation property?

Yes. In tracking queries, related entities materialized through the navigation query can be connected to already tracked entities through relationship fixup.

### When should I use explicit loading instead of eager loading?

Use eager loading when you already know the related data is needed with the initial query. Use explicit loading when the main entity has already been retrieved and the application should decide later whether additional related data is needed.

### Can the Change Tracker connect related entities loaded by separate queries?

Yes. When change tracking is enabled, EF Core can perform relationship fixup between newly materialized entities and related entities that are already being tracked.

