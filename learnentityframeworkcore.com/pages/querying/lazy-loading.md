---
title: Lazy Loading in EF Core
description: Learn how to lazy-load related data in EF Core with proxies, ILazyLoader, and delegates, and understand the N+1 query risk.
canonical: /querying/lazy-loading
status: Published
lastmod: 2026-09-03
---

# Lazy Loading Related Data in Entity Framework Core

Lazy loading can automatically load related data when a configured navigation property is accessed. Unlike [Eager Loading](/querying/eager-loading), which requests related data as part of the initial query operation, or [Explicit Loading](/querying/explicit-loading), which loads it later through an explicit operation, lazy loading can retrieve it on demand when the navigation is used.

In EF Core, lazy loading requires a configured mechanism, and the simplest way to enable it is by using lazy-loading proxies.

## Lazy Loading with Proxies

To use lazy-loading proxies, install the `Microsoft.EntityFrameworkCore.Proxies` package:

```bash
dotnet add package Microsoft.EntityFrameworkCore.Proxies
```

Then enable lazy-loading proxies when configuring the `DbContext`:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlite("Data Source=blogs.db");
}
```

You can also call `UseLazyLoadingProxies()` when configuring the context with `AddDbContext`.

Lazy-loading proxies work by creating derived versions of your entity types at runtime. For EF Core to intercept a navigation property, that navigation must be overridable. In practice, this means declaring it as `virtual` on a class that can be inherited from:

```csharp
public class Blog
{
    public int BlogId { get; set; }

    public string Name { get; set; } = null!;

    public virtual ICollection<Post> Posts { get; set; } = [];
}

public class Post
{
    public int PostId { get; set; }

    public string Title { get; set; } = null!;

    public int BlogId { get; set; }

    public virtual Blog Blog { get; set; } = null!;
}
```

Once lazy-loading proxies are configured, related data can be loaded when the application accesses an unloaded navigation:

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

Console.WriteLine(blog.Name);

foreach (var post in blog.Posts)
{
    Console.WriteLine(post.Title);
}
```

The query above loads the `Blog` entity without explicitly requesting its related `Posts`.

When `blog.Posts` is accessed, the proxy intercepts the navigation property. If the navigation still needs to be loaded and lazy loading can be performed, EF Core queries the database for the related `Post` entities and populates the collection.

This is the key behavior of lazy loading: related data can be retrieved later, when the navigation property is accessed, instead of being explicitly requested as part of the initial query operation.

If you already know that the related data should be loaded with the main entity, see [Eager Loading](/querying/eager-loading) and [Include](/querying/include) instead.

## Requirements for Lazy-Loading Proxies

The proxy-based example above already uses inheritable entity types and `virtual` navigations.

Constructors used by the proxy must also be accessible from the derived proxy type, normally `public` or `protected`.

A navigation that cannot be overridden cannot be lazy-loaded through a proxy.

### Non-Virtual Navigations

Since EF Core 8, proxy configuration can be instructed to ignore non-virtual navigations that should not participate in lazy loading:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseLazyLoadingProxies(
            options => options.IgnoreNonVirtualNavigations())
        .UseSqlite("Data Source=blogs.db");
}
```

With this configuration, EF Core ignores non-virtual navigations instead of treating them as proxy configuration errors. Accessing those navigations does not trigger lazy loading through the proxy.

### Configuring `UseLazyLoadingProxies`

Calling `UseLazyLoadingProxies()` without arguments enables lazy-loading proxies:

```csharp
optionsBuilder.UseLazyLoadingProxies();
```

The API also provides a Boolean overload. Passing `false` disables the use of lazy-loading proxies:

```csharp
optionsBuilder.UseLazyLoadingProxies(false);
```

Since EF Core 8, proxy-specific configuration can use the overload that accepts an options action:

```csharp
optionsBuilder.UseLazyLoadingProxies(
    options => options.IgnoreNonVirtualNavigations());
```

Equivalent overloads are also available for `DbContextOptionsBuilder<TContext>`.

## Lazy Loading without Proxies

Lazy loading does not require proxies. EF Core can also inject the `ILazyLoader` service into an entity and use it to load a navigation property when its getter is accessed.

Unlike proxy-based lazy loading, this approach does not require the entity class to be inheritable or the navigation property to be `virtual`.

The `ILazyLoader` service is defined in the `Microsoft.EntityFrameworkCore.Abstractions` package. If the project containing your entity classes does not already reference it, install the package:

```bash
dotnet add package Microsoft.EntityFrameworkCore.Abstractions
```

The entity receives an `ILazyLoader` through its constructor and uses a backing field for the navigation property:

```csharp
using Microsoft.EntityFrameworkCore.Infrastructure;

public class Blog
{
    private ICollection<Post>? _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; } = null!;

    public int BlogId { get; set; }

    public string Name { get; set; } = null!;

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts)!;
        set => _posts = value;
    }
}
```

Here, `Posts` is not `virtual`. Instead, its getter calls `ILazyLoader.Load`, which gives EF Core an opportunity to load the related posts when the navigation is accessed.

The application can then use the navigation in the same way:

```csharp
var blog = await context.Blogs
    .SingleAsync(blog => blog.BlogId == 1);

foreach (var post in blog.Posts)
{
    Console.WriteLine(post.Title);
}
```

When `blog.Posts` is accessed and the navigation still needs to be loaded, the injected `ILazyLoader` can retrieve the related `Post` entities.

This approach avoids runtime proxy subclasses, but the entity class depends directly on the EF Core `ILazyLoader` service.

## Lazy Loading with a Delegate

If you want lazy loading without proxies but also want to avoid referencing EF Core types such as `ILazyLoader` in the entity class, EF Core can inject the lazy-loading method as a delegate.

The constructor receives an `Action<object, string>` instead of an `ILazyLoader`:

```csharp
public class Blog
{
    private ICollection<Post>? _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string>? LazyLoader { get; set; }

    public int BlogId { get; set; }

    public string Name { get; set; } = null!;

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}
```

The `Load` call above uses a small extension method:

```csharp
using System.Runtime.CompilerServices;

public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string>? loader,
        object entity,
        ref TRelated? navigationField,
        [CallerMemberName] string navigationName = "")
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField!;
    }
}
```

This pattern provides the same on-access loading behavior while keeping EF Core types out of the entity class.

> **Note:** By convention, the constructor parameter for the lazy-loading delegate must be named `lazyLoader`.

## Controlling Lazy Loading

After a lazy-loading mechanism has been configured, you can control whether lazy loading is used for specific navigations or for the current `DbContext`.

### Disable Lazy Loading for a Navigation

Since EF Core 8, use `EnableLazyLoading(false)` to prevent a specific navigation from being lazy-loaded:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Navigation(blog => blog.Posts)
        .EnableLazyLoading(false);
}
```

With this configuration, accessing `blog.Posts` does not trigger lazy loading for that navigation.

`EnableLazyLoading` has an optional Boolean parameter. Calling it without an argument enables lazy loading for the navigation, while passing `false` disables it.

This setting does not configure lazy loading by itself. A lazy-loading mechanism, such as proxies or `ILazyLoader`, must already be configured.

### Disable Lazy Loading for the Current Context

Lazy loading can also be enabled or disabled through the context's `ChangeTracker`:

```csharp
context.ChangeTracker.LazyLoadingEnabled = false;
```

Setting `LazyLoadingEnabled` to `false` prevents lazy loading through that context.

The property defaults to `true`, but this does not mean that lazy loading is automatically enabled in every EF Core application. Lazy loading still requires a configured mechanism such as lazy-loading proxies or `ILazyLoader`.

## Lazy Loading and the N+1 Problem

Lazy loading can make related data convenient to access, but it can also cause many additional database queries when a navigation is accessed repeatedly for multiple entities.

For example:

```csharp
var blogs = await context.Blogs
    .OrderBy(blog => blog.BlogId)
    .ToListAsync();

foreach (var blog in blogs)
{
    Console.WriteLine($"{blog.Name}: {blog.Posts.Count} posts");
}
```

The first query retrieves the `Blog` entities.

Then, when `blog.Posts` is accessed for individual blogs, lazy loading can execute an additional query to retrieve that blog's posts. If the initial query returns five blogs whose `Posts` navigations still need to be loaded, the application can execute:

```text
1 query to load the blogs
+ 5 queries to load their posts
= 6 database queries
```

This pattern is commonly called the **N+1 query problem**: one query retrieves the main entities, followed by additional queries as related data is accessed for individual results.

The risk comes from allowing lazy loading to trigger database access repeatedly while processing a result set. With larger result sets, these additional roundtrips can become expensive and may be easy to overlook.

If you already know that the related data will be needed, consider loading it as part of the initial query operation instead. See [Eager Loading](/querying/eager-loading) and [Include](/querying/include).

If you want to decide later whether related data should be loaded while keeping the database operation explicit in your code, see [Explicit Loading](/querying/explicit-loading).

## Important Lazy Loading Behavior

A few behaviors affect when lazy loading can occur and are important to understand when using it in real applications.

### Lazy Loading with No-Tracking Queries

Since EF Core 8, entities returned by no-tracking queries can also lazy-load configured navigations.

For example:

```csharp
var blog = await context.Blogs
    .AsNoTracking()
    .SingleAsync(blog => blog.BlogId == 1);

foreach (var post in blog.Posts)
{
    Console.WriteLine(post.Title);
}
```

If `Posts` supports lazy loading, accessing the navigation can still load the related posts even though the `Blog` entity is not tracked.

In this scenario, the entity retains access to the `DbContext` that queried it so that lazy loading can occur.

For more information about tracking and no-tracking queries, see [Query Tracking](/querying/query-tracking).

### The `DbContext` Must Still Be Available

Lazy loading can only retrieve related data while the `DbContext` associated with the entity is still available.

Once that context has been disposed, an unloaded navigation can no longer be lazy-loaded from the database.

This matters when entities remain in use beyond the lifetime of the context that queried them.

### Lazy Loading Uses Synchronous I/O

Lazy loading is triggered by accessing a navigation property. Because property access cannot be awaited, database I/O initiated by lazy loading is synchronous.

If you need to decide explicitly when related data is loaded and want to await that database operation, see [Explicit Loading](/querying/explicit-loading), which supports methods such as `LoadAsync()`.

## Lazy Loading vs. Eager Loading vs. Explicit Loading

EF Core supports different strategies for loading related data. The main difference is **when and how** the related data is requested.

| Loading strategy | When related data is loaded |
| ---------------- | --------------------------- |
| Eager loading | Requested as part of the initial query operation |
| Explicit loading | Loaded after the main entity has been retrieved, through an explicit database operation |
| Lazy loading | Loaded later when a configured navigation property is accessed |

Use [Eager Loading](/querying/eager-loading) when you already know that related data is needed with the main entity.

Use [Explicit Loading](/querying/explicit-loading) when the decision should happen later but the database operation should remain explicit in application code.

Use lazy loading when related data may be needed later and implicit database access on navigation access is acceptable.

## When Should You Use Lazy Loading?

Lazy loading can be useful when related data is needed only in some code paths and implicit loading when a navigation is accessed is acceptable.

The tradeoff is that navigation access can trigger database activity that is not visible in the query that originally loaded the entity. This makes lazy loading less suitable when predictable queries and database roundtrips are important.

Consider the loading strategy based on when your application knows that related data is needed:

* Use **lazy loading** when related data may be needed and implicit loading on navigation access is acceptable.
* Use [Eager Loading](/querying/eager-loading) when you already know that related data will be needed with the main entity.
* Use [Explicit Loading](/querying/explicit-loading) when you want to decide later whether to load related data while keeping the database call explicit in your code.
* Use [Projection](/querying/projection) when you only need specific values rather than complete related entities.

Lazy loading is a poor fit when database access needs to remain easy to identify and control.

## What Lazy Loading Does Not Do

Lazy loading does not mean that related data is retrieved with the initial query operation. A configured navigation is loaded later when it is accessed and still needs to be loaded.

It also does not eliminate database access: navigation access can still trigger an additional query.

## External Resources - Lazy Loading

The following videos complement the examples in this article with practical demonstrations of lazy loading in EF Core. Together, they cover proxy-based configuration, navigation access that triggers additional queries, the N+1 query pattern, and lazy loading without proxies.

### Video 1 - Don't SUCK With Entity Framework - Lazy Loading Proxies - Performance Tips Part 2

<iframe width="560" height="315" src="https://www.youtube.com/embed/9z_uk7ZrON0" title="Don't SUCK With Entity Framework - Lazy Loading Proxies - Performance Tips Part 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video demonstrates lazy loading with proxies and makes the implicit database access especially easy to see by pairing the code with SQL Server Profiler.

The selected segments focus on the proxy requirements, `UseLazyLoadingProxies()`, the navigation access that triggers lazy loading, and the additional SQL generated by that access.

**Key timestamps:**

* [0:58](https://www.youtube.com/watch?v=9z_uk7ZrON0&t=58s) — Shows `virtual` navigation properties, which allow EF Core proxies to intercept navigation access.
* [2:02](https://www.youtube.com/watch?v=9z_uk7ZrON0&t=122s) — Shows `UseLazyLoadingProxies()` configured on the `DbContext`.
* [3:14](https://www.youtube.com/watch?v=9z_uk7ZrON0&t=194s) — Accesses `cat.Owner.Name`, illustrating the navigation access that can trigger lazy loading.
* [4:52](https://www.youtube.com/watch?v=9z_uk7ZrON0&t=292s) — Shows SQL Server Profiler after the navigation access, making the additional database query visible.

The video uses an older EF Core version and includes some broad performance recommendations, so the selected timestamps focus specifically on proxy configuration and query-on-access behavior that remain relevant to the concepts covered in this article.

### Video 2 - (4)- Lazy Loading and the n+1 problem

<iframe width="560" height="315" src="https://www.youtube.com/embed/KHkj6i1Q_54" title="(4)- Lazy loading y el problema n+1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video focuses on the N+1 query pattern caused by lazy loading and makes the repeated database access visible through the generated SQL logs.

The example accesses a related `Invoices` collection inside a loop, then compares that behavior with an eager-loading version that uses `Include`.

**Key timestamps:**

* [2:51](https://www.youtube.com/watch?v=KHkj6i1Q_54&t=171s) — Shows `item.Invoices` being accessed inside the loop, which triggers lazy loading for the related invoices.
* [3:49](https://www.youtube.com/watch?v=KHkj6i1Q_54&t=229s) — Shows the repeated SQL commands generated as the navigation is accessed for multiple items, illustrating the N+1 query pattern.
* [5:24](https://www.youtube.com/watch?v=KHkj6i1Q_54&t=324s) — Shows the result after switching the example to eager loading with `Include`, allowing the query behavior to be compared with the lazy-loading version.

The video presents avoiding lazy loading as a general recommendation and describes the `Include` version as using a single query. The selected timestamps focus specifically on the navigation-access, N+1, and comparison behavior demonstrated in this example.

### Video 3 - Entity Framework Core #15 - Lazy Loading

<iframe width="560" height="315" src="https://www.youtube.com/embed/m5t1qYfZVQI" title="Entity Framework Core #15 - Lazy Loading" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This Portuguese-language video demonstrates lazy loading without proxies. It shows constructor injection with a lazy-loading delegate, a navigation getter that calls `Load`, and the additional SQL generated when that navigation is accessed.

The video also discusses `ILazyLoader`, but the selected segments focus on its concrete delegate-based example.

**Key timestamps:**

* [10:50](https://www.youtube.com/watch?v=m5t1qYfZVQI&t=650s) — Introduces a second example that implements lazy loading without proxies.
* [11:55](https://www.youtube.com/watch?v=m5t1qYfZVQI&t=715s) — Shows constructor injection using an `Action<object, string>` lazy-loading delegate.
* [13:19](https://www.youtube.com/watch?v=m5t1qYfZVQI&t=799s) — Shows the navigation getter calling `LazyLoader.Load(this, ref _emprestimos)` to load the related collection on access.
* [20:14](https://www.youtube.com/watch?v=m5t1qYfZVQI&t=1214s) — Shows SQL Server Profiler after the navigation is accessed, where an additional query is issued for the related `LivroEmprestimo` entities.

The video uses EF Core 2.1 and an older implementation style. The selected timestamps focus on the delegate-based approach and query-on-access behavior that remain useful for understanding lazy loading without proxies.

## Summary

Lazy loading lets related data be retrieved later when a configured navigation is accessed.

* Lazy-loading proxies require `Microsoft.EntityFrameworkCore.Proxies`, `UseLazyLoadingProxies()`, inheritable entity types, and overridable navigation properties.
* `ILazyLoader` and delegate-based loading provide lazy loading without requiring proxy subclasses or `virtual` navigations.
* Since EF Core 8, proxy behavior can be configured to ignore non-virtual navigations, and individual navigations can opt out of lazy loading with `EnableLazyLoading(false)`.
* Lazy loading can also be controlled for a context with `ChangeTracker.LazyLoadingEnabled`.
* No-tracking entities can lazy-load configured navigations in EF Core 8 and later while their associated `DbContext` remains available.
* Repeated navigation access across multiple entities can create an N+1 query pattern, so implicit database access should be used intentionally.

## Related Articles

Lazy loading is one of several ways EF Core can work with related data. The following articles explain neighboring querying and loading behaviors in more detail:

* [Eager Loading in EF Core](/querying/eager-loading) — Load related data together with the main entity.
* [Explicit Loading in EF Core](/querying/explicit-loading) — Load related data later while keeping the database operation explicit.
* [Include in EF Core](/querying/include) — Use `Include` and `ThenInclude` to specify related data in the initial query.
* [Projection in EF Core](/querying/projection) — Select only the data your query needs instead of loading complete entities.
* [Query Tracking in EF Core](/querying/query-tracking) — Understand tracking and no-tracking queries, including the behavior of queried entities.

## FAQ

### Is lazy loading enabled by default in EF Core?

EF Core does not lazy-load related data unless a lazy-loading mechanism such as proxies or `ILazyLoader` has been configured.

### Do navigation properties need to be `virtual` for lazy loading?

Only when using lazy-loading proxies. `ILazyLoader` and delegate-based lazy loading do not require navigation properties to be `virtual`.

### Does accessing a navigation always execute another database query?

No. A lazy-loading query is needed only when the navigation still needs to be loaded and lazy loading can be performed.

### Can lazy loading work with `AsNoTracking()`?

Yes. Since EF Core 8, entities returned by no-tracking queries can lazy-load configured navigations while the associated `DbContext` remains available.

### Why can lazy loading cause the N+1 problem?

When code loads multiple main entities and then accesses an unloaded navigation for individual results, lazy loading can execute additional queries for those navigations.

### What is the difference between lazy loading and explicit loading?

Lazy loading can retrieve related data implicitly when a navigation is accessed. [Explicit Loading](/querying/explicit-loading) makes the database operation visible in application code and allows asynchronous methods such as `LoadAsync()`.