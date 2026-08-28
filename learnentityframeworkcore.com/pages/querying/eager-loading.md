---
title: Eager Loading in EF Core
description: Learn how eager loading works in EF Core, when to use Include, how it differs from explicit and lazy loading, and how tracking and split queries affect related data.
canonical: /querying/eager-loading
status: Published
lastmod: 2026-08-28
---

# Eager Loading in EF Core

Eager loading in Entity Framework Core loads related data as part of the query operation that retrieves the main entities. Use it when you already know which related data the application will need when you build the query.

The most common way to eager load related data is with [`Include`](/querying/include).

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

In this query:

* `context.Blogs` is the query source.
* `Include(blog => blog.Posts)` tells EF Core to load the related `Posts` for each returned `Blog`.
* `Include` composes the query; it does not execute it.
* `ToListAsync()` executes the query and materializes the results.

The result is a `List<Blog>`. The root result type does not change because of `Include`; instead, EF Core also populates the `Posts` navigation of the returned `Blog` entities with the related posts that were eagerly loaded.

## How Eager Loading Works

Eager loading is about deciding **before the query is executed** which related data should be loaded together with the main entities.

For the relationship:

```text
Blog
 └── Posts
```

`Blog` is the main entity being queried, and `Posts` is a collection navigation.

When the query executes, EF Core retrieves the `Blog` entities and the related data needed to populate the requested `Posts` navigation.

Eager loading is not limited to collection navigations. It can also load a reference navigation, where an entity points to a single related entity:

```text
Blog
 └── Owner
```

`Blog.Posts` is a collection navigation because one blog can have many posts. `Blog.Owner` is a reference navigation because each blog has one related owner.

In both cases, the important idea is the same: the application specifies the related data it needs as part of the query before that query is executed.

The returned entities keep their normal entity types. Eager loading populates their navigation properties rather than projecting the query into a different result shape.

## Eager Load More Complex Relationship Graphs

Eager loading is not limited to a single navigation. A query can load several related navigations from the same entity or follow relationships through multiple levels of the entity graph.

### Load Multiple Relationships

An application may need more than one related navigation for the same main entity.

For example, the following query eagerly loads both the `Posts` collection and the `Owner` reference for each blog:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Owner)
    .ToListAsync();
```

Both navigations are specified before the query is executed. When `ToListAsync()` completes, each returned `Blog` has both its related `Posts` and its `Owner` loaded.

The root result is still a `List<Blog>`; eager loading adds related entities to the navigation properties of those blogs rather than changing the root result type.

### Load Multiple Levels of Related Data

Eager loading can also follow relationships beyond the first navigation.

If a `Blog` has many `Posts` and each `Post` has an `Author`, you can load both levels:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
        .ThenInclude(post => post.Author)
    .ToListAsync();
```

The loaded entity graph can be represented as:

```text
Blog
 └── Posts
      └── Author
```

In this case, EF Core loads the `Blog` entities, their related `Posts`, and the `Author` associated with each post.

The root result remains a `List<Blog>`, while the related entities are available through their corresponding navigation properties.

## Learn More About Include

`Include` and `ThenInclude` are the main APIs used to specify which navigations should be eagerly loaded. The detailed syntax and rules belong to the dedicated [Include in EF Core](/querying/include) article.

That article explains how to:

* use [`Include`](/querying/include#load-related-data-with-include) to load a related navigation;
* use [`ThenInclude`](/querying/include#load-nested-related-data-with-theninclude) to continue through additional relationship levels;
* load multiple navigation paths;
* filter, order, and limit included collections;
* configure navigations with `AutoInclude`;
* disable configured auto-includes with `IgnoreAutoIncludes`.

Eager loading describes the **loading strategy**: related data is requested as part of the query for the main entities.

`Include`, `ThenInclude`, and related APIs specify **which navigations** EF Core should load using that strategy.

## Important Eager Loading Behavior

Several behaviors are useful to understand before relying on eager loading in an application.

### Include Does Not Execute the Query

`Include` changes the query definition but does not retrieve data immediately.

```csharp
var query = context.Blogs
    .Include(blog => blog.Posts);
```

At this point, `query` remains an `IQueryable<Blog>`.

The database query is executed when the application requests a result:

```csharp
var blogs = await query.ToListAsync();
```

This is the same deferred-execution behavior used by other EF Core query operators. See [LINQ Queries in EF Core](/querying/linq-queries) for more about building and executing queries.

### Eager Loading Does Not Change the Root Result Type

Adding `Include` does not turn the result into a DTO or another projected result shape.

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

The application still receives a `List<Blog>`. The difference is that the requested `Posts` navigation is also populated with the related entities loaded by the query.

### Tracking Behavior Also Applies to Related Entities

EF Core entity queries are tracking by default.

For a normal tracking query, the root entities and the related entities materialized through eager loading are tracked by the current `DbContext`.

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

The returned `Blog` and eagerly loaded `Post` entities participate in the normal tracking behavior.

If the query uses `AsNoTracking()`, the returned entity graph is not tracked by the current context:

```csharp
var blogs = await context.Blogs
    .AsNoTracking()
    .Include(blog => blog.Posts)
    .ToListAsync();
```

Use the tracking behavior that matches what the application needs to do with the returned entities. See [Query Tracking in EF Core](/querying/query-tracking) for the available tracking options.

### Eager Loading Does Not Mean One SQL Query

Eager loading determines **what related data should be loaded**, not how many database commands EF Core must use to retrieve it.

A query can eagerly load the same entity graph with single-query or [split-query](/querying/split-queries) behavior.

That distinction becomes especially important when loading collections and is covered in [Eager Loading and Query Behavior](#eager-loading-and-query-behavior).

## Eager Loading vs. Explicit Loading vs. Lazy Loading

EF Core supports three common patterns for loading related data: **eager loading**, **explicit loading**, and **lazy loading**.

The main difference between them is **when the related data is loaded**.

| Loading strategy     | When is related data loaded?           | Typical behavior                                                   |
| -------------------- | -------------------------------------- | ------------------------------------------------------------------ |
| **Eager loading**    | As part of the initial query operation | The related navigations are specified before the query is executed |
| **Explicit loading** | After the main entity has been loaded  | Your code explicitly requests a navigation when it is needed       |
| **Lazy loading**     | When a navigation property is accessed | EF Core automatically loads the related data at that point         |

### Eager Loading

With eager loading, the application decides which related data it needs while building the original query.

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

When the query operation completes, the returned blogs already have the requested posts loaded.

### Explicit Loading

With explicit loading, the main entity is loaded first and the application explicitly requests a navigation later.

Conceptually:

```text
Load Blog
    ↓
Decide that Posts are needed
    ↓
Explicitly load Posts
```

EF Core provides APIs through `DbContext.Entry(...)` for explicitly loading reference and collection navigations.

> Explicit Loading is covered separately in [Explicit Loading in EF Core](/querying/explicit-loading).

### Lazy Loading

With lazy loading, related data is loaded automatically when the application accesses a navigation property.

Conceptually:

```text
Load Blog
    ↓
Access blog.Posts
    ↓
EF Core loads Posts automatically
```

Unlike eager loading, the original query does not need to specify that navigation.

Lazy loading requires additional configuration, such as lazy-loading proxies or `ILazyLoader`, depending on the approach used.

> Lazy Loading is covered separately in **Lazy Loading in EF Core** *(Coming soon)*.

## Eager Loading vs. Projection

Eager loading and projection can both retrieve data from related entities, but they serve different purposes.

Use eager loading when the application needs **entity instances with their navigation properties populated**:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

The result is a `List<Blog>` containing `Blog` entities and their eagerly loaded `Post` entities.

Use projection when the application only needs selected values or a custom result shape:

```csharp
var blogs = await context.Blogs
    .Select(blog => new
    {
        blog.Url,
        PostTitles = blog.Posts
            .Select(post => post.Title)
            .ToList()
    })
    .ToListAsync();
```

This result does not need complete `Blog` and `Post` entities just to expose the selected values.

In short:

* use eager loading when you need related **entities in navigation properties**;
* use projection when you need only specific **values or a custom result shape**.

See [Projection in EF Core](/querying/projection) for more about shaping related data with `Select`.

## When Should You Use Eager Loading?

Use eager loading when the operation already knows which related entities it will need when the query is built.

Typical examples include:

* displaying an order together with its order lines;
* loading a blog together with posts that will immediately be shown;
* retrieving an entity together with a related reference needed by the same operation;
* loading a known entity graph that will be processed together;
* querying related entities that the application intends to modify within the same tracked unit of work.

For example, if a page always displays blogs together with their posts, expressing that requirement directly in the query makes the required graph clear:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

The query itself communicates that the operation requires both the blogs and their related posts.

Eager loading is particularly useful when loading the related data later would add unnecessary decision points or separately triggered database access.

## When Not to Use Eager Loading

Eager loading is not automatically the best choice whenever a relationship exists.

Avoid loading related entities eagerly when the operation does not actually need them.

For example, do not load complete `Blog` and `Post` entities when the result only needs a blog URL and the number of posts. A [projection](/querying/projection) can usually express that result more directly.

Be cautious with eager loading when:

* the result only needs a few scalar values from the related entity;
* the related data is optional for the current operation and may never be used;
* a large entity graph would be loaded only for convenience;
* several large collections would significantly increase the amount of data retrieved;
* a projection can express the required result without materializing complete related entities.

If related data should be loaded only after the application decides it is necessary, explicit loading may better match the workflow.

If automatic loading on navigation access is intentionally desired, lazy loading may be another option.

The goal is not to eager load every available navigation. Load the graph that the current operation actually needs.

## Eager Loading and Query Behavior

The shape of the eagerly loaded graph can affect how much data the database must return and how EF Core executes the query.

### Eager Loading Collections

A reference navigation normally contributes at most one related entity for each relationship. A collection navigation can contain many related entities.

For example:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .ToListAsync();
```

A blog with many posts requires many related rows to be retrieved.

The effect can become more significant when multiple collection navigations are eagerly loaded:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Contributors)
    .ToListAsync();
```

`Posts` and `Contributors` are sibling collections of `Blog`:

```text
Blog
├── Posts
└── Contributors
```

With single-query behavior on a relational provider, the joins for sibling collections can multiply rows in the database result.

For example, if one blog has 10 posts and 10 contributors, the relational result can contain 100 rows for that blog because each post is combined with each contributor.

This behavior is commonly called **cartesian explosion**.

The application still receives the expected `Blog` entity graph, but the database may need to return substantially more data to construct it.

### Use Split Queries for an Alternative Execution Strategy

EF Core supports split-query behavior for queries that load related collections.

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Contributors)
    .AsSplitQuery()
    .ToListAsync();
```

With split-query behavior, EF Core can retrieve the root entities and related collections through separate database queries and assemble the entity graph from the results.

This is still **eager loading**.

The application specified the related data before execution; only the database execution strategy changed.

> **Eager loading determines what related data is requested. Single-query and split-query behavior determine how EF Core retrieves that data from the database.**

Split queries also have trade-offs, including additional database round trips and possible consistency considerations between queries. Do not assume that split queries are always faster.

For a detailed explanation of `AsSplitQuery`, `AsSingleQuery`, cartesian explosion, and the trade-offs between both strategies, see [Split Queries in EF Core](/querying/split-queries).

### Navigation Fixup

Tracking queries have another behavior that can affect navigation properties.

EF Core performs **navigation fixup**, which connects related entities already tracked by the same `DbContext`.

Because of this, a navigation can contain related entities that were already tracked from an earlier query, in addition to the entities materialized by the current query.

This is especially important with filtered includes. Previously tracked related entities can appear in an included navigation even when they do not satisfy the filter used by the current query.

For example, a long-lived context may already track posts that were loaded by an earlier query. A later tracking query with a filtered include can reconnect those tracked posts to their blogs through navigation fixup.

This does not change the purpose of eager loading, but it means that the final contents of a navigation in a tracking context can also be influenced by entities the `DbContext` already knows about.

For more about this behavior, see [Query Tracking in EF Core](/querying/query-tracking). For the specific rules of filtered include, see [Include in EF Core](/querying/include#filtered-include-and-tracking).

## Common Pitfalls

Most eager-loading problems come from loading more data than the operation needs or making incorrect assumptions about how related data is retrieved.

### Eager Loading Every Navigation

Do not add `Include` calls simply because navigation properties exist.

For example:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Owner)
    .Include(blog => blog.Contributors)
    .ToListAsync();
```

This may be appropriate when the operation truly needs all three relationships.

If it only needs the blog URL and owner name, loading complete entities and additional collections may retrieve more data than necessary.

Choose the entity graph based on the current operation rather than loading every available navigation by default.

### Assuming Eager Loading Always Uses One SQL Query

Eager loading is a loading strategy, not a guarantee about the number of SQL commands.

A query using `AsSplitQuery()` is still performing eager loading even though EF Core executes multiple database queries.

### Using Include When Only Selected Values Are Needed

`Include` is useful when the result needs related entities populated in navigation properties.

If the operation only needs selected values, use `Select` to project those values instead.

See [Projection in EF Core](/querying/projection).

### Ignoring the Cost of Multiple Collections

Loading several sibling collections through a single query can produce a large relational result because of row multiplication.

Consider the actual graph size and use [Split Queries](/querying/split-queries) when that execution strategy better fits the query.

### Assuming a Tracking Navigation Contains Only Data from the Current Query

Navigation fixup can associate previously tracked related entities with entities returned by the current query.

This is particularly important with filtered include.

When the navigation must represent only the current filtered query, consider a new `DbContext` or a no-tracking query when tracking is not required.

## External Resources - Eager Loading

The following videos provide practical demonstrations of eager loading and related data-loading strategies in EF Core. They complement the examples in this article with visual walkthroughs of `Include`, nested relationships, projection, explicit loading, lazy loading, and the SQL behavior behind these approaches.

### Video 1 - Eager, Explicit & Lazy Loading in EF Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/LSL3Bgf_nno" title="Eager, Explicit & Lazy Loading in EF Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This video by Remigiusz Zalewski compares eager, explicit, and lazy loading in EF Core using a practical e-commerce example and shows the generated SQL behavior for each strategy.

**Key timestamps:**

* [**05:07**](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=307s) — Introduces eager loading and builds a related-data query with `Include` and multiple `ThenInclude` calls.
* [**08:39**](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=519s) — Executes the eager-loading query and inspects the loaded entity graph after materialization.
* [**13:02**](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=782s) — Demonstrates explicit loading with `Entry(...).Collection(...).LoadAsync()` and later a reference navigation.
* [**16:36**](https://www.youtube.com/watch?v=LSL3Bgf_nno&t=996s) — Explains lazy loading and shows how accessing related data can lead to the N+1 query problem.

The video uses EF Core 10 and is especially useful for comparing the three loading strategies and seeing how they affect query execution.

### Video 2 - 4 Formas de Cargar Data Relacionada en Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/1G-z4xdg0pw" title="4 Formas de Cargar Data Relacionada en Entity Framework Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This Spanish-language video by Felipe Gavilán demonstrates several ways to retrieve related data in EF Core and is particularly useful for comparing eager loading with projection.

**Key timestamps:**

* [**01:52**](https://www.youtube.com/watch?v=1G-z4xdg0pw&t=112s) — Starts the eager-loading example and loads related movie data with `Include`.
* [**03:07**](https://www.youtube.com/watch?v=1G-z4xdg0pw&t=187s) — Uses `ThenInclude` to continue through a nested relationship.
* [**07:37**](https://www.youtube.com/watch?v=1G-z4xdg0pw&t=457s) — Shows how `Select` can project only the related values the result needs instead of loading complete related entities.
* [**22:21**](https://www.youtube.com/watch?v=1G-z4xdg0pw&t=1341s) — Demonstrates how lazy loading can be triggered indirectly during mapping and lead to many additional database queries.

The strongest contribution of this video is the contrast between eager loading and projection, showing when loading complete related entities may be unnecessary.

### Video 3 - Entendiendo Lazy Loading, Eager Loading y Explicit Loading en Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/Tt0zQgfEWTw" title="Entendiendo Lazy Loading, Eager Loading y Explicit Loading en Entity Framework Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This Spanish-language video by NetMentor provides a concise introduction to the three main related-data loading strategies in EF Core and shows how each one affects database access.

**Key timestamps:**

* [**01:23**](https://www.youtube.com/watch?v=Tt0zQgfEWTw&t=83s) — Demonstrates basic eager loading with `Include` on a collection navigation.
* [**05:20**](https://www.youtube.com/watch?v=Tt0zQgfEWTw&t=320s) — Shows how lazy loading triggers an additional database query when a related navigation is accessed.
* [**06:12**](https://www.youtube.com/watch?v=Tt0zQgfEWTw&t=372s) — Demonstrates the N+1 pattern by accessing a related navigation inside a loop.
* [**08:46**](https://www.youtube.com/watch?v=Tt0zQgfEWTw&t=526s) — Shows explicit loading with `Entry(...).Collection(...).LoadAsync()`.

This video is especially useful as a shorter, beginner-friendly comparison of eager, lazy, and explicit loading, with clear visual examples of when additional SQL queries are executed.

## Summary

Eager loading retrieves related data as part of the query operation when the application already knows which relationships it needs.

Key points:

* use `Include` for a related navigation and `ThenInclude` for deeper navigation paths;
* eager loading populates navigation properties without changing the root entity result type;
* `Include` composes the query but does not execute it;
* tracking behavior also applies to eagerly loaded entities;
* eager loading determines **what** related data is requested, while single- and split-query behavior determine **how** it is retrieved;
* prefer projection when the operation needs selected values rather than complete related entities.

For detailed `Include`, `ThenInclude`, filtered include, `AutoInclude`, and `IgnoreAutoIncludes` examples, see [Include in EF Core](/querying/include).

## Related Articles

To continue exploring how EF Core loads, shapes, and tracks related data, these articles are the most relevant next steps:

* [Include in EF Core](/querying/include) — Learn how to use `Include`, `ThenInclude`, multiple include paths, filtered includes, `AutoInclude`, and `IgnoreAutoIncludes`.
* [Projection in EF Core](/querying/projection) — Learn how to return selected values and custom result shapes without loading complete related entities.
* [Split Queries in EF Core](/querying/split-queries) — Learn when and how to retrieve related collections using multiple database queries.
* [Query Tracking in EF Core](/querying/query-tracking) — Understand tracking, no-tracking queries, identity resolution, and how tracked entities affect query results.
* [LINQ Queries in EF Core](/querying/linq-queries) — Learn how EF Core queries are built, composed, and executed.
* [Explicit Loading in EF Core](/querying/explicit-loading) — Load related data explicitly after the main entities have been retrieved.
* **Lazy Loading in EF Core** *(Coming soon)* — Load related data automatically when a navigation property is accessed.

## FAQ

### What is eager loading in EF Core?

Eager loading retrieves related data as part of the query operation for the main entities, so the requested navigation properties are populated when the result is materialized.

### What is the difference between eager loading and Include?

Eager loading is the loading strategy. `Include` is one of the main EF Core APIs used to specify which navigation properties should be eagerly loaded.

For detailed `Include` patterns, see [Include in EF Core](/querying/include).

### Does Include execute the query?

No. `Include` composes the query by specifying related data to load. The query executes when a terminal operation such as `ToListAsync()` requests the result.

### Are eagerly loaded entities tracked?

By default, yes. The root entities and eagerly loaded related entities participate in the normal tracking behavior of the query.

Use `AsNoTracking()` when the returned entity graph does not need to be tracked. See [Query Tracking in EF Core](/querying/query-tracking).

### Do I need Include when projecting related data?

No. If the result only needs selected values from related data, use `Select` to project those values instead of loading complete related entities.

See [Projection in EF Core](/querying/projection).

### Does eager loading always use one SQL query?

No. Eager loading determines which related data should be loaded, not how many SQL queries EF Core uses to retrieve it.

Related data can be retrieved using single-query or split-query behavior. See [Split Queries in EF Core](/querying/split-queries).

### What is the difference between eager, explicit, and lazy loading?

The main difference is when related data is loaded:

* **Eager loading:** specified as part of the initial query operation.
* **Explicit loading:** requested explicitly after the main entity has been loaded.
* **Lazy loading:** loaded automatically when a navigation property is accessed.

### When should I use eager loading?

Use eager loading when you already know while building the query which related entities the operation will need.

If related data is only sometimes needed, explicit loading may better match the workflow. If you only need selected values, projection is usually a better fit.