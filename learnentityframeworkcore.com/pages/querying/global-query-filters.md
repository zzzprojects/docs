---
title: Global Query Filters in EF Core
description: Learn how to automatically filter EF Core queries with global query filters, including soft delete, multi-tenancy, multiple and named filters, and IgnoreQueryFilters.
canonical: /querying/global-query-filters
status: Published
lastmod: 2026-09-22
---

# Global Query Filters in EF Core

Global query filters in Entity Framework Core let you define a filter for an entity type and have EF Core apply it by default when that entity type participates in a query.

They are useful when the same filtering rule should apply across many [LINQ queries](/querying/linq-queries), such as excluding soft-deleted rows or limiting data to the current tenant.

A common example is soft delete:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasQueryFilter(product => !product.IsDeleted);
}
```

With this filter configured, the application can query `Product` entities normally:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

EF Core applies the configured filter as part of the query, so products where `IsDeleted` is `true` are excluded without adding the same `Where` condition to every query.

## Use HasQueryFilter with Soft Delete

Soft delete keeps a row in the database instead of physically removing it. An `IsDeleted` property records whether the entity should normally be excluded from application queries.

For example:

```csharp
public class Product
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }
    public bool IsDeleted { get; set; }
}
```

Configure the global query filter for `Product` in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasQueryFilter(product => !product.IsDeleted);
}
```

`HasQueryFilter` associates the predicate with the `Product` entity type. In this case, the filter keeps products whose `IsDeleted` value is `false`.

Consider the following data:

| ProductId | Name | Price | IsDeleted |
| ---: | --- | ---: | --- |
| 1 | Laptop | 1200.00 | false |
| 2 | Mouse | 25.00 | true |
| 3 | Keyboard | 80.00 | false |

The application does not need to repeat the soft-delete condition when querying products:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

The result contains:

```text
Laptop
Keyboard
```

`Mouse` is not returned because the configured global query filter excludes products where `IsDeleted` is `true`.

The result is still a `List<Product>`. A global query filter changes which rows are returned; it does not change the result shape.

Conceptually, the filtering behavior is similar to adding the condition explicitly:

```csharp
var products = await context.Products
    .Where(product => !product.IsDeleted)
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

The difference is that with `HasQueryFilter`, the filtering rule is part of the EF Core model and does not need to be repeated in each normal query.

`HasQueryFilter` controls which entities queries return by default. It does not by itself change a delete operation into a soft delete; the application must separately decide how and when `IsDeleted` is set to `true`.

## How Global Query Filters Work

Once a global query filter is part of the model, it participates in queries for that entity type unless it is explicitly disabled.

You can continue composing additional query conditions normally:

```csharp
var products = await context.Products
    .Where(product => product.Price >= 50)
    .OrderBy(product => product.Name)
    .ToListAsync();
```

The explicit `Where` condition does not replace the global query filter. Both conditions participate in the query: the global filter excludes deleted products, while the explicit filter keeps products whose price is at least 50.

`HasQueryFilter` configures model behavior; it does not execute a database query. In the example above, the query is executed when `ToListAsync()` materializes the results.

Global query filters are commonly used for:

* **soft delete** — exclude entities marked as deleted;
* **multi-tenancy** — restrict queries to data associated with the current tenant.

Filters can also be combined, named, and selectively disabled. The following sections build on this first example to show those behaviors.

## Use HasQueryFilter with Multi-Tenancy

Global query filters can also depend on data stored in the current `DbContext` instance.

A common example is multi-tenancy, where rows in the same table belong to different tenants and queries should normally return only data for the current tenant.

Add a `TenantId` property to `Product`:

```csharp
public class Product
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }
    public bool IsDeleted { get; set; }
    public string TenantId { get; set; } = null!;
}
```

The current tenant identifier can be supplied when the context is created:

```csharp
public class AppDbContext(string tenantId) : DbContext
{
    public DbSet<Product> Products => Set<Product>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Product>()
            .HasQueryFilter(product => product.TenantId == tenantId);
    }
}
```

The query filter references the `tenantId` associated with that context instance. Different context instances can therefore use different tenant values while the filtering rule remains part of the model configuration.

The application can then query products normally:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

EF Core incorporates the global filter into the query expression, which the database provider must be able to translate as part of the generated query. By default, the results are therefore limited to products whose `TenantId` matches the tenant associated with the current context.

For example, consider this data:

| ProductId | Name | TenantId |
| ---: | --- | --- |
| 1 | Laptop | tenant-a |
| 2 | Mouse | tenant-b |
| 3 | Keyboard | tenant-a |

When the context is created with `tenant-a`, the query returns:

```text
Laptop
Keyboard
```

The result is still a `List<Product>`. The tenant filter affects which rows are returned, not the result shape.

This example shows only the global query filter behavior needed for multi-tenancy. Resolving tenants, authenticating users, provisioning tenant data, and choosing a broader multi-tenancy architecture are separate concerns.

## Use Multiple Query Filters

An application may need more than one global filtering rule for the same entity.

For example, `Product` may need both:

* a soft-delete filter that excludes deleted products;
* a tenant filter that returns only products belonging to the current tenant.

A first attempt might be to configure both filters separately:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasQueryFilter(product => !product.IsDeleted);

    modelBuilder.Entity<Product>()
        .HasQueryFilter(product => product.TenantId == tenantId);
}
```

Calling `HasQueryFilter` twice without names does not mean that both filters are applied.

When `HasQueryFilter` is called again without a filter name for the same entity type, the new filter replaces the previous one.

In this example, the tenant filter replaces the soft-delete filter. Deleted products could therefore still be returned as long as they belong to the current tenant.

When both conditions need to be represented in a single unnamed filter, combine them in the same predicate:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasQueryFilter(product =>
            !product.IsDeleted &&
            product.TenantId == tenantId);
}
```

Now both conditions participate in normal `Product` queries:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

Conceptually, a product is returned only when both conditions are true:

```text
IsDeleted = false
    AND
TenantId = current tenant
```

This solves the filtering problem, but not the management problem. EF Core treats both conditions as one filter definition, so they cannot be disabled independently.

EF Core 10 named query filters address that limitation by allowing multiple filters to be defined separately on the same entity type.

## Use Named Query Filters

EF Core 10 allows multiple query filters to be configured separately on the same entity type by assigning each filter a name.

For example, `Product` can have one filter for soft delete and another for multi-tenancy:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasQueryFilter("SoftDeleteFilter",
            product => !product.IsDeleted)
        .HasQueryFilter("TenantFilter",
            product => product.TenantId == tenantId);
}
```

Each call defines a separate global query filter.

Normal `Product` queries use both filters:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

By default, a returned product must satisfy both configured filters:

```text
SoftDeleteFilter:
IsDeleted = false

    AND

TenantFilter:
TenantId = current tenant
```

Unlike a single unnamed filter containing both conditions, named filters can be managed independently.

That distinction becomes important when a query needs to temporarily include data that one of the filters would normally exclude.

## Ignore All Query Filters

Use `IgnoreQueryFilters()` when a specific query should bypass all applicable global query filters.

For example:

```csharp
var products = await context.Products
    .IgnoreQueryFilters()
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

With both `SoftDeleteFilter` and `TenantFilter` configured, this query disables both filters.

That means the result can include:

* products marked as deleted;
* products belonging to other tenants.

`IgnoreQueryFilters()` changes the query being built; it does not remove the filters from the EF Core model.

Other queries remain unaffected. A normal `context.Products` query continues using the configured filters.

Like other query operators, `IgnoreQueryFilters()` does not execute the query. `ToListAsync()` executes it and materializes the result.

## Ignore Specific Named Query Filters

EF Core 10 also allows a query to disable selected named filters while leaving the others active.

Pass the names of the filters to disable to `IgnoreQueryFilters`.

For example, an administrative operation may need to include soft-deleted products while still restricting results to the current tenant:

```csharp
var products = await context.Products
    .IgnoreQueryFilters(["SoftDeleteFilter"])
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

This disables only `SoftDeleteFilter`.

`TenantFilter` remains active, so products from other tenants are still excluded.

The filter name passed to `IgnoreQueryFilters` must match the name used when the filter was configured.

Conceptually, the query behavior becomes:

```text
SoftDeleteFilter:
IGNORED

TenantFilter:
TenantId = current tenant
```

You can also disable more than one named filter by passing multiple names:

```csharp
var products = await context.Products
    .IgnoreQueryFilters(["SoftDeleteFilter", "TenantFilter"])
    .ToListAsync();
```

In this case, both named filters are disabled for that query.

The key difference is:

| Query | Filter behavior |
| --- | --- |
| Normal query | All configured filters apply |
| `IgnoreQueryFilters()` | All applicable query filters are disabled |
| `IgnoreQueryFilters(["SoftDeleteFilter"])` | Only `SoftDeleteFilter` is disabled |
| `IgnoreQueryFilters(["SoftDeleteFilter", "TenantFilter"])` | The specified named filters are disabled |

Selective disabling requires named query filters. If soft delete and tenant filtering are combined into a single unnamed predicate, EF Core cannot disable only one of those conditions independently.

## Important Global Query Filter Behavior

Global query filters become part of the query model for an entity, so they can also affect queries that involve relationships and navigations.

One behavior worth understanding is how filters interact with required relationships.

### Global Query Filters and Required Navigations

When a relationship is required, the dependent entity is expected to have a related principal. When a query loads that relationship, a relational provider may use an `INNER JOIN`.

If the related entity is excluded by a global query filter, the `INNER JOIN` can also remove the root entity from the result.

Consider a `Category` with related `Product` entities:

```csharp
public class Category
{
    public int CategoryId { get; set; }
    public string Name { get; set; } = null!;
    public bool IsActive { get; set; }

    public List<Product> Products { get; set; } = [];
}

public class Product
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;

    public int CategoryId { get; set; }
    public Category Category { get; set; } = null!;
}
```

The relationship is configured as required, and inactive categories are filtered out:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasOne(product => product.Category)
        .WithMany(category => category.Products)
        .HasForeignKey(product => product.CategoryId)
        .IsRequired();

    modelBuilder.Entity<Category>()
        .HasQueryFilter(category => category.IsActive);
}
```

A query for `Product` does not directly apply the `Category` filter:

```csharp
var products = await context.Products
    .ToListAsync();
```

However, when the query loads the required `Category` navigation:

```csharp
var products = await context.Products
    .Include(product => product.Category)
    .ToListAsync();
```

the required relationship may be translated using an `INNER JOIN`.

If an inactive category is excluded by its global query filter, products associated with that category can also disappear from the result because the join no longer finds a matching category row.

This can result in fewer `Product` entities being returned when the related navigation is included.

For more about loading related data with `Include`, see [Include in EF Core](/querying/include).

If the relationship is genuinely optional, model it as optional so a relational provider can use a `LEFT JOIN` rather than requiring a matching related row.

Another approach is to configure consistent query filters on both entity types when the application expects both sides of the relationship to follow the same visibility rule.

The important point is not that required navigations are incorrect. It is that a global filter on a required related entity can also affect which root entities are returned.

## Query Translation and Provider Behavior

A global query filter is expressed as a LINQ predicate, and that predicate must participate in query translation when the entity is queried.

Simple expressions such as:

```csharp
product => product.TenantId == tenantId
```

are typical filter predicates.

More complex C# logic may not be translatable by a database provider. Avoid assuming that an expression that works with LINQ to Objects will necessarily translate successfully as part of an EF Core query.

The exact query generated from a global filter can also vary by provider.

## Requirements and Limitations

Global query filters apply broadly, so several limitations are important when designing them.

`HasQueryFilter` also exposes `LambdaExpression` overloads, including named-filter variants, for scenarios where the filter expression is built dynamically. The strongly typed expressions shown in this article are clearer for normal `EntityTypeBuilder<TEntity>` configuration.

### Configure Filters on the Root Type of an Inheritance Hierarchy

Global query filters can only be defined on the root entity type of an inheritance hierarchy.

If several entity types participate in the same hierarchy, configure the global filter on the root type rather than attempting to define separate filters on derived types.

### Avoid Cycles Between Query Filters

EF Core does not currently detect cycles between global query filter definitions.

Filters that reference navigations or other filtered entities can create circular dependencies if they are designed incorrectly, potentially causing problems during query translation.

Keep related filter definitions consistent and avoid circular dependencies between them.

### Context-Dependent Filters Need Context Data

A filter such as:

```csharp
product => product.TenantId == tenantId
```

depends on contextual information.

The required value must be accessible from the `DbContext` instance so the filter can use it when queries are created for that context.

The multi-tenancy example earlier supplies the tenant identifier through the context constructor, keeping that dependency explicit.

### Ignoring Filters Is a Query-Level Decision

`IgnoreQueryFilters()` affects the query on which it is used.

It does not remove the configured filters from the EF Core model or change the default behavior of later queries.

This makes it useful for deliberate exceptions, but bypassing a filter should be intentional, especially when filters are used to separate tenant data or hide soft-deleted entities.

## External Resources - Global Query Filters

The following videos provide practical demonstrations of global query filters in EF Core. They complement the examples in this article with visual walkthroughs of `HasQueryFilter`, soft delete, multi-tenancy, named query filters, `IgnoreQueryFilters`, and the SQL behavior behind these filtering patterns.

### Video 1 - Named Query Filters in EF Core 10 Are a Game Changer

<iframe width="560" height="315" src="https://www.youtube.com/embed/SSV7yaH4n9g" title="Named Query Filters in EF Core 10 Are a Game Changer" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This video by Milan Jovanović demonstrates the limitation of unnamed query filters and shows how EF Core 10 named query filters allow multiple filters to be configured and selectively disabled.

**Key timestamps:**

* [**02:11**](https://www.youtube.com/watch?v=SSV7yaH4n9g&t=131s) — Shows the problem with defining multiple unnamed query filters on the same entity and how the later filter replaces the previous one.
* [**05:19**](https://www.youtube.com/watch?v=SSV7yaH4n9g&t=319s) — Introduces EF Core 10 named query filters and configures separate soft-delete and tenant filters.
* [**06:12**](https://www.youtube.com/watch?v=SSV7yaH4n9g&t=372s) — Shows the difference between ignoring all query filters and selectively disabling specific named filters.
* [**06:54**](https://www.youtube.com/watch?v=SSV7yaH4n9g&t=414s) — Demonstrates ignoring only the soft-delete filter while the tenant filter remains active.

The video was recorded while EF Core 10 was still in preview, but the named query filter behavior demonstrated here is part of EF Core 10 and is especially useful for understanding why named filters provide more control than a single combined filter.

### Video 2 - Why your Entity Framework Core app needs query filters

<iframe width="560" height="315" src="https://www.youtube.com/embed/lxfce9HQCHs" title="Why Your Entity Framework Core App Needs Query Filters" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This video from Round The Code gives a compact, practical overview of global query filters, moving from soft delete to multi-tenancy and then to the EF Core 10 named filter improvements.

**Key timestamps:**

* [**01:24**](https://www.youtube.com/watch?v=lxfce9HQCHs&t=84s) — Introduces global query filters and uses `HasQueryFilter` to exclude soft-deleted products without repeating the same `Where` condition in every query.
* [**03:57**](https://www.youtube.com/watch?v=lxfce9HQCHs&t=237s) — Shows a multi-tenancy scenario where context data is used so queries return only products for the current site or tenant.
* [**06:10**](https://www.youtube.com/watch?v=lxfce9HQCHs&t=370s) — Explains the limitation of multiple unnamed filters and introduces named query filters in EF Core 10.
* [**07:16**](https://www.youtube.com/watch?v=lxfce9HQCHs&t=436s) — Demonstrates selective `IgnoreQueryFilters`, disabling one named filter while leaving another filter active.

The strongest contribution of this video is its end-to-end progression from a repeated filtering problem to soft delete, tenant filtering, and independently manageable named filters.

### Video 3 - How To Apply Global Filters With EF Core Query Filters

<iframe width="560" height="315" src="https://www.youtube.com/embed/q09fw5OCa_w" title="How To Apply Global Filters With EF Core Query Filters" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This video by Milan Jovanović explains the core behavior of EF Core query filters, including how `HasQueryFilter` removes repeated filtering logic, how the filter appears in generated SQL, and how `IgnoreQueryFilters` changes a specific query.

**Key timestamps:**

* [**03:21**](https://www.youtube.com/watch?v=q09fw5OCa_w&t=201s) — Replaces repeated query conditions with a single `HasQueryFilter` configuration.
* [**04:28**](https://www.youtube.com/watch?v=q09fw5OCa_w&t=268s) — Demonstrates how the global query filter is applied automatically and appears in the SQL generated by EF Core.
* [**06:15**](https://www.youtube.com/watch?v=q09fw5OCa_w&t=375s) — Introduces `IgnoreQueryFilters()` to disable the configured filter for a specific query.
* [**08:21**](https://www.youtube.com/watch?v=q09fw5OCa_w&t=501s) — Shows what happens when an explicit query condition conflicts with the global query filter: both conditions are applied, which can make the query return no rows.

This video is especially useful for seeing how query filters participate in the generated SQL and how global filtering interacts with additional conditions written directly in a query.

## Summary

Global query filters let you define filtering rules once in the EF Core model and have those rules participate in queries by default.

Key points:

* use `HasQueryFilter` to configure a global filter for an entity type;
* soft delete and multi-tenancy are common scenarios for global filters;
* calling unnamed `HasQueryFilter` more than once for the same entity replaces the previous filter;
* EF Core 10 named query filters allow multiple filters to be configured and managed independently;
* use `IgnoreQueryFilters()` to disable all applicable filters for a query;
* use `IgnoreQueryFilters([...])` in EF Core 10 to disable selected named filters;
* global filters can affect results that involve required navigations;
* filter expressions must be translatable by the database provider;
* global query filters can only be configured on the root entity type of an inheritance hierarchy.

## Related Articles

Continue with these related EF Core querying topics:

* [LINQ Queries in EF Core](/querying/linq-queries) — Learn how EF Core queries are composed, executed, and materialized.
* [Include in EF Core](/querying/include) — Learn how to load related navigations and how `Include` affects query composition.
* [Eager Loading in EF Core](/querying/eager-loading) — Understand eager loading as a strategy for retrieving related data together with the main entities.

## FAQ

### What is a global query filter in EF Core?

A global query filter is a LINQ predicate configured for an entity type with `HasQueryFilter`.

EF Core includes that predicate in queries for the entity type by default, so the same filtering rule does not need to be repeated manually in every query.

### Can I configure more than one global query filter for the same entity?

Yes, but the configuration matters.

Calling unnamed `HasQueryFilter` more than once for the same entity replaces the previously configured filter.

You can either combine multiple conditions into a single predicate or, in EF Core 10, configure separate named query filters.

### What is the difference between IgnoreQueryFilters() and IgnoreQueryFilters([...])?

`IgnoreQueryFilters()` disables all applicable global query filters for that query.

In EF Core 10, `IgnoreQueryFilters([...])` can disable only the named filters specified in the collection while leaving other named filters active.

### Can global query filters be used for multi-tenancy?

Yes.

A global query filter can reference tenant information available from the current `DbContext` and restrict queries to rows associated with that tenant.

The filter handles query filtering only; tenant resolution, authentication, authorization, and the broader multi-tenancy architecture remain separate concerns.

### Can a global query filter affect related entities?

Yes.

A filter on a related entity can affect the root query result when required navigations are involved. For relational providers, a required relationship may be translated with an `INNER JOIN`, so filtering out the related entity can also remove the root row from the result.

See [Include in EF Core](/querying/include) for more about queries that load related navigations.