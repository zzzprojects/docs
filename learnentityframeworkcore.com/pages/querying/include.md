---
title: Include in EF Core
description: Learn how to use Include and ThenInclude in EF Core to load related data, filter included collections, and configure AutoInclude.
canonical: /querying/include
status: Published
lastmod: 2026-08-07
---

# Include in EF Core

Entity Framework Core uses `Include` to load related data together with the root entities returned by a query. Use `ThenInclude` when you need to continue loading data through a nested relationship.

## Load Related Data with Include

Use `Include` to specify a navigation property that EF Core should load with the root entities.

The following example loads blogs together with their related posts:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

`context.Blogs` is the query source, and `Include(blog => blog.Posts)` adds the `Posts` collection navigation to the `IQueryable`.

`Include` does not execute the query by itself. EF Core executes the query when `ToListAsync()` is called and materializes the results as a list of `Blog` entities.

After the query completes, the `Posts` navigation is loaded for each returned blog. This query does not request other navigations, such as `Owner` or `Theme`, unless they are also included or configured with [`AutoInclude`](#configure-autoinclude).

`Include` can also load a reference navigation. The following query loads each blog together with its owner:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Owner)
    .ToListAsync();
```

`Owner` is a reference navigation to one entity, while `Posts` is a collection navigation. EF Core uses the same `Include` method for both navigation types.

## Load Nested Related Data with ThenInclude

Use `ThenInclude` to continue a navigation path from the relationship selected by the preceding `Include`.

The following query loads blogs, their posts, and the author of each post:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
        .ThenInclude(post => post.Author)
    .ToListAsync();
```

`Include(blog => blog.Posts)` starts the path from `Blog` through the `Posts` collection. `ThenInclude(post => post.Author)` continues from each `Post` to its `Author` reference.

The complete navigation path is:

```text
Blog → Posts → Author
```

After `ToListAsync()` executes the query, the `Posts` navigation is loaded for each returned blog, and the `Author` navigation is loaded for each included post.

Additional `ThenInclude` calls can continue through deeper relationships when the query needs more levels of related data.

## Include Multiple Relationships

Add multiple `Include` calls when a query needs more than one navigation from the root entity.

The following example loads each blog together with its owner, theme, and posts:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Owner)
    .Include(blog => blog.Theme)
    .Include(blog => blog.Posts)
    .ToListAsync();
```

Each `Include` defines a separate navigation path starting from `Blog`:

```text
Blog → Owner
Blog → Theme
Blog → Posts
```

All three paths remain part of the same `IQueryable` until `ToListAsync()` executes the query. The returned `Blog` entities then have the `Owner`, `Theme`, and `Posts` navigations loaded.

### Include Multiple Paths from the Same Navigation

When an included entity has more than one navigation that must be loaded, specify each complete path from the root entity.

The following query loads the author and tags for each post:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
        .ThenInclude(post => post.Author)
    .Include(blog => blog.Posts)
        .ThenInclude(post => post.Tags)
    .ToListAsync();
```

The query contains two navigation paths:

```text
Blog → Posts → Author
Blog → Posts → Tags
```

The first path loads the `Author` reference for each post. The second path loads the `Tags` collection.

Repeat the shared `Blog → Posts` path so that each `ThenInclude` can continue through a different navigation from `Post`.

EF Core processes the complete set of include paths when the query is executed. The exact SQL and number of database commands can vary by provider, query shape, and query-splitting configuration.

## Filter Related Data with Include

Use filtered include to filter, order, or limit the entities loaded into an included collection navigation.

The following query loads each blog together with its five most recent published posts:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished)
        .OrderByDescending(post => post.PublishedOn)
        .ThenBy(post => post.PostId)
        .Take(5))
    .ToListAsync();
```

The `Where`, `OrderByDescending`, `ThenBy`, and `Take` operations apply to the `Posts` collection included for each blog.

They do not filter the root `Blog` entities. The query can still return a blog that has no published posts; in that case, its included `Posts` collection is empty.

To filter the blogs themselves, apply `Where` to the root query:

```csharp
var blogs = await context.Blogs
    .Where(blog => blog.Posts.Any(post => post.IsPublished))
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished)
        .OrderByDescending(post => post.PublishedOn)
        .ThenBy(post => post.PostId)
        .Take(5))
    .ToListAsync();
```

The first `Where` determines which blogs are returned. The operations inside `Include` determine which posts are loaded into the `Posts` collection of each returned blog.

### Supported Filtered Include Operations

EF Core supports the following operations on a collection navigation inside `Include`:

* [`Where`](/querying/linq-methods#where)
* [`OrderBy`](/querying/linq-methods#orderby)
* [`OrderByDescending`](/querying/linq-methods#orderbydescending)
* [`ThenBy`](/querying/linq-methods#thenby)
* [`ThenByDescending`](/querying/linq-methods#thenbydescending)
* [`Skip`](/querying/linq-methods#skip)
* [`Take`](/querying/linq-methods#take)

These operations must be applied to a collection navigation in the expression passed to `Include`.

Not every LINQ operator can be used inside a filtered include. For example, filtered include is not a general-purpose projection and does not support arbitrary operators such as `Select` inside the navigation expression.

The database provider must also be able to translate the complete query when it is executed.

### Filter the Same Navigation More Than Once

A collection navigation can appear in multiple include paths, but it can have only one unique set of filter operations in the same query.

The following query filters `Posts` in one path and continues through two different relationships:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished))
        .ThenInclude(post => post.Author)
    .Include(blog => blog.Posts)
        .ThenInclude(post => post.Tags)
    .ToListAsync();
```

Both paths include the same `Posts` navigation. The filter controls that included collection, while each `ThenInclude` continues from the included posts through a different relationship.

You can also repeat the filter on both paths, but the operations must be identical:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished))
        .ThenInclude(post => post.Author)
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished))
        .ThenInclude(post => post.Tags)
    .ToListAsync();
```

Do not configure different sets of filter operations for the same included collection navigation in one query.

### Filtered Include and Tracking

Filtered include requires additional care in tracking queries.

When the current `DbContext` is already tracking related entities, navigation fixup can add those entities to the included navigation even when they do not match the current filter.

For example:

```csharp
await context.Posts
    .Where(post => !post.IsPublished)
    .ToListAsync();

var blogs = await context.Blogs
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished))
    .ToListAsync();
```

The first query loads unpublished posts into the Change Tracker. When the second tracking query runs, navigation fixup can associate those previously tracked posts with their blogs, even though the filtered include requests only published posts.

When the collection must reflect only the current filtered query, use a new `DbContext` or consider a no-tracking query when tracking is not required:

```csharp
var blogs = await context.Blogs
    .AsNoTracking()
    .Include(blog => blog.Posts
        .Where(post => post.IsPublished))
    .ToListAsync();
```

In a tracking query, EF Core also considers a filtered navigation to be loaded. Even when the filter returns only part of the related collection, explicit loading or lazy loading will not automatically retrieve the missing entities later.

## Configure AutoInclude

Use `AutoInclude` to configure a navigation that should be loaded automatically whenever its entity type is returned in a query result.

The following model configuration automatically includes the `ColorScheme` navigation whenever EF Core returns a `Theme` entity:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Theme>()
        .Navigation(theme => theme.ColorScheme)
        .AutoInclude();
}
```

After this configuration, the following query loads themes together with their color schemes without an explicit `Include` call:

```csharp
var themes = await context.Themes
    .ToListAsync();
```

`AutoInclude` is configured in the EF Core model rather than on an individual query. It has an effect similar to adding `Include(theme => theme.ColorScheme)` without having to specify it in each query.

The configuration also applies when a `Theme` entity appears in the result through another navigation or another auto-included relationship. For example, if a query includes a blog's `Theme`, the configured `ColorScheme` navigation is also loaded for that theme.

Use `AutoInclude` deliberately. Because the configuration applies broadly, queries can load the navigation even when a particular operation does not need it.

## Suppress AutoInclude for a Query with IgnoreAutoIncludes

Use `IgnoreAutoIncludes()` when a particular query should not request navigations configured with `AutoInclude`.

The following query returns themes without requesting the `ColorScheme` navigation through its `AutoInclude` configuration:

```csharp
var themes = await context.Themes
    .IgnoreAutoIncludes()
    .ToListAsync();
```

In this query, `IgnoreAutoIncludes()` suppresses the user-configured auto-include for `ColorScheme`.

`IgnoreAutoIncludes()` affects only the query on which it is called. It does not remove or modify the `AutoInclude` configuration in the EF Core model.

However, navigations to owned types are auto-included by convention and are not suppressed by `IgnoreAutoIncludes()`. They remain included in the query results.

## External Resources - Include

The following videos are useful if you want to see `Include`, `ThenInclude`, filtered include, and `AutoInclude` in practical EF Core examples. The first focuses on loading related data through navigation paths and configuring automatic includes. The second helps illustrate the difference between filtering the root query and filtering the related collection inside `Include`.

### Video 1 - .NET 6 EF Core 🚀 Load Related Data with Include(), ThenInclude() & AutoInclude()

<iframe width="560" height="315" src="https://www.youtube.com/embed/7HuOivcr6Mg" title=".NET 6 EF Core 🚀 Load Related Data with Include(), ThenInclude() & AutoInclude()" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Patrick God demonstrates how to load related entities with `Include`, continue through a navigation path with `ThenInclude`, and configure `AutoInclude` in the EF Core model. The video uses .NET 6 and demonstrates APIs that remain applicable to EF Core 10.

**Key timestamps:**

* [9:14](https://www.youtube.com/watch?v=7HuOivcr6Mg&t=554s) — Loading a related collection with `Include()`
* [10:49](https://www.youtube.com/watch?v=7HuOivcr6Mg&t=649s) — Continuing the navigation path with `ThenInclude()`
* [13:22](https://www.youtube.com/watch?v=7HuOivcr6Mg&t=802s) — Configuring related data to load automatically with `AutoInclude()`

### Video 2 - EF Core 5 - Many-to-Many - Filter Include - 4 Examples of Related Data Queries

<iframe width="560" height="315" src="https://www.youtube.com/embed/OeOymtdQagw" title="EF Core 5 - Many-to-Many - Filter Include - 4 Examples of Related Data Queries" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Felipe Gavilan demonstrates related-data queries using a many-to-many `Movie` and `Genre` model. The selected segments show the progression from loading a related collection with `Include`, to filtering the root entities by related data, and finally filtering the contents of the included collection with `Where`.

The video uses .NET 5 and EF Core 5. The selected filtered include pattern remains relevant to EF Core 10, while this article reflects the current EF Core 10 behavior and limitations.

**Key timestamps:**

* [4:02](https://www.youtube.com/watch?v=OeOymtdQagw&t=242s) — Loading the related `Genres` collection with `Include`
* [5:10](https://www.youtube.com/watch?v=OeOymtdQagw&t=310s) — Filtering the root `Movie` query by related genre data
* [6:18](https://www.youtube.com/watch?v=OeOymtdQagw&t=378s) — Filtering the included `Genres` collection with `Where`

## Summary

Use `Include` to load a reference or collection navigation and `ThenInclude` to continue through nested relationships. These methods compose the `IQueryable`; EF Core executes it when a terminal method such as `ToListAsync()` requests the results.

Add multiple include paths when the result requires several navigations. Filtered include can limit an included collection with supported filtering, ordering, `Skip`, and `Take` operations. In tracking queries, previously tracked entities can affect the collection through navigation fixup, and EF Core considers the filtered navigation loaded.

Use `AutoInclude` to configure a navigation that should normally be loaded whenever its entity type is returned. Use `IgnoreAutoIncludes()` to suppress application-configured auto-includes for a particular query, except for navigations to owned types that EF Core includes by convention.

## Related Articles

* [LINQ Queries](/querying/linq-queries) — Learn how to build, compose, and execute EF Core queries.
* [LINQ Methods](/querying/linq-methods) — Learn how to use individual LINQ operators with EF Core.

## FAQ

### Does `Include` execute the query immediately?

No. `Include` adds a navigation to the `IQueryable` but does not execute the query. EF Core retrieves and materializes the results when a terminal method such as `ToListAsync()` is called.

### Can I use more than one `Include` in the same query?

Yes. Add multiple `Include` calls to load several navigations from the root entity. When different navigation paths continue from the same relationship, repeat the shared path and use `ThenInclude` for each branch.

### Does filtered include filter the root entities?

No. Operations inside `Include` limit the entities loaded into the included collection. Apply `Where` to the root query when you also need to control which root entities are returned.

### Can I use any LINQ operator inside a filtered include?

No. EF Core supports `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take` on an included collection navigation.

### Does `IgnoreAutoIncludes()` disable every automatically included navigation?

No. It suppresses navigations explicitly configured with `AutoInclude`, but navigations to owned types that EF Core auto-includes by convention remain included.