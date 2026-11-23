---
title: Specialized Entity Types in EF Core
description: Learn how to configure keyless and shared-type entity types, indexer properties, and property bag entity types in EF Core.
canonical: /modeling/specialized-entity-type
status: Published
lastmod: 2026-11-23
---

# Specialized Entity Types in EF Core

Entity Framework Core supports specialized entity types for scenarios that go beyond conventional entity mapping. For example, a **keyless entity type** allows you to query data without a primary key.

To map a keyless entity type to an existing database view, use `HasNoKey()` and `ToView()` in `OnModelCreating`:

```csharp
modelBuilder.Entity<ProductSummary>(entity =>
{
    entity.HasNoKey();
    entity.ToView("ProductSummaries");
});
```

`HasNoKey()` configures `ProductSummary` without a primary key, while `ToView()` maps it to the `ProductSummaries` database view. Keyless entity types are never tracked for changes. For other mapping options, see the [official EF Core documentation on keyless entity types](https://learn.microsoft.com/en-us/ef/core/modeling/keyless-entity-types?tabs=data-annotations).

EF Core also supports shared-type entity types, indexer properties, and property bag entity types. The following table helps you identify when each feature is useful:

| When you need to... | Use |
|---|---|
| Query data without a primary key. | **Keyless Entity Type** |
| Map different entity types using the same CLR type. | **Shared-Type Entity Type** |
| Store and access mapped properties through a C# indexer. | **Indexer Properties** |
| Represent an entity using a dictionary of property names and values. | **Property Bag Entity Type** |

These features address different modeling needs and are not necessarily mutually exclusive.

## Keyless Entity Types

Keyless entity types are useful for querying database views, tables without primary keys, or results from raw SQL queries.

For example, suppose the `ProductSummaries` database view returns each category's name and its total number of products.

Define a class to represent the results:

```csharp
public class ProductSummary
{
    public string CategoryName { get; set; } = null!;
    public int ProductCount { get; set; }
}
```

The configuration shown earlier maps this class to the existing `ProductSummaries` view. EF Core uses the mapped columns to populate `CategoryName` and `ProductCount`. The view must already exist in the database because `ToView()` does not create it.

Unlike regular entity types, keyless entity types cannot have a primary key and are never tracked for changes. Therefore, they cannot be inserted, updated, or deleted through EF Core's normal change-tracking operations.

You can also use the `[Keyless]` attribute instead of `HasNoKey()` or map a keyless entity type to a read-only table.

If you need to execute SQL queries that return mapped entities, see [FromSql in EF Core](/querying/from-sql).

## Shared-Type Entity Types

A shared-type entity type allows multiple entity types in the EF Core model to use the same CLR class. Each entity type has a unique name that EF Core uses to distinguish it from the others.

For example, suppose you want to represent products and services using the same `CatalogItem` class while storing them in separate database tables:

```csharp
public class CatalogItem
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
}
```

You can configure both entity types in `OnModelCreating` using `SharedTypeEntity<T>()`:

```csharp
modelBuilder.SharedTypeEntity<CatalogItem>("Product", entity =>
{
    entity.HasKey(e => e.Id);
    entity.ToTable("Products");
});

modelBuilder.SharedTypeEntity<CatalogItem>("Service", entity =>
{
    entity.HasKey(e => e.Id);
    entity.ToTable("Services");
});
```

Although both entity types use the same `CatalogItem` class, EF Core treats `Product` and `Service` as distinct entity types. Each has its own name, primary key configuration, and table mapping.

Because the CLR type alone cannot identify which entity type you want to access, you must specify its name when obtaining the corresponding `DbSet`:

```csharp
var products = context.Set<CatalogItem>("Product");
var services = context.Set<CatalogItem>("Service");
```

This allows you to work with each entity type independently while reusing the same CLR class.

For additional configuration details, see the [official EF Core documentation on shared-type entity types](https://learn.microsoft.com/en-us/ef/core/modeling/entity-types?tabs=data-annotations#shared-type-entity-types).

## Indexer Properties

Indexer properties allow you to map additional properties to an entity using a C# indexer instead of declaring a separate CLR property for each value.

For example, suppose you want to store a product's last update date without adding a `LastUpdated` property directly to the `Product` class.

First, define an indexer that stores values in a private dictionary:

```csharp
public class Product
{
    private readonly Dictionary<string, object> _data = new();

    public int Id { get; set; }
    public string Name { get; set; } = null!;

    public object this[string key]
    {
        get => _data[key];
        set => _data[key] = value;
    }
}
```

Then, configure `LastUpdated` as an indexer property in `OnModelCreating`:

```csharp
modelBuilder.Entity<Product>()
    .IndexerProperty<DateTime>("LastUpdated");
```

EF Core maps `LastUpdated` to a database column, but its value is stored in the `Product` instance through the indexer rather than a regular CLR property.

You can set and read the value using the indexer:

```csharp
var product = new Product { Name = "Laptop" };

product["LastUpdated"] = DateTime.UtcNow;

var lastUpdated = (DateTime)product["LastUpdated"];
```

Unlike [shadow properties](/model/shadow-properties), whose values are maintained by EF Core's change tracker, indexer properties store their values in the entity instance itself.

For additional details, see the [official EF Core documentation on indexer properties](https://learn.microsoft.com/en-us/ef/core/modeling/shadow-properties#configuring-indexer-properties).

## Property Bag Entity Types

Unlike the previous `Product` example, which uses a dictionary to store additional values, a property bag entity type uses `Dictionary<string, object>` to represent the entire entity.

Property bag entity types combine shared-type entity configuration with indexer properties, allowing EF Core to map an entity without requiring a separate CLR class with declared properties.

For example, suppose you want to represent product metadata using a dictionary.

You can configure a property bag entity type in `OnModelCreating`:

```csharp
modelBuilder.SharedTypeEntity<Dictionary<string, object>>(
    "ProductMetadata", entity =>
    {
        entity.IndexerProperty<int>("Id");
        entity.HasKey("Id");

        entity.IndexerProperty<string>("Description");
    });
```

`SharedTypeEntity<Dictionary<string, object>>()` registers `ProductMetadata` as a named entity type. The `IndexerProperty<T>()` calls configure the mapped properties, while `HasKey()` identifies `Id` as the primary key.

You can represent an instance using a dictionary:

```csharp
var metadata = new Dictionary<string, object>
{
    ["Id"] = 1,
    ["Description"] = "Electronics"
};
```

Because `ProductMetadata` is a shared-type entity type, you must specify its name when accessing the corresponding `DbSet`:

```csharp
var metadataSet = context.Set<Dictionary<string, object>>("ProductMetadata");
```

Unlike regular entity types, property bag entity types do not support shadow properties, indexer navigations, or inheritance.

For more details and additional limitations, see the [official EF Core documentation on property bag entity types](https://learn.microsoft.com/en-us/ef/core/modeling/shadow-properties#property-bag-entity-types).

## External Resources - Specialized Entity Types

The following videos complement the examples in this article with practical demonstrations of specialized entity types in EF Core. They show how to map keyless entities to database views and explore how shared-type entities, indexer properties, and dictionary-based join entities work together.

### Video 1 - Using a View in Entity Framework Core - Entity Framework Core Tutorial

<iframe width="560" height="315" src="https://www.youtube.com/embed/p1gA1iX081A" title="Uso de una vista en Entity Framework Core - Tutorial de Entity Framework Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In this tutorial, Coding Under Pressure demonstrates how to map an existing SQLite database view in EF Core. The example uses a view that combines car models and colors, showing how to define a corresponding CLR class, configure the mapping with `HasNoKey()` and `ToView()`, and query the results.

**Key timestamps:**

- **[02:29](https://www.youtube.com/watch?v=p1gA1iX081A&t=149s)** — Defines the CLR class representing the database view's results.
- **[03:49](https://www.youtube.com/watch?v=p1gA1iX081A&t=229s)** — Explains why the entity must be configured without a primary key.
- **[04:44](https://www.youtube.com/watch?v=p1gA1iX081A&t=284s)** — Configures `HasNoKey()` and `ToView()` in `OnModelCreating()`.
- **[06:45](https://www.youtube.com/watch?v=p1gA1iX081A&t=405s)** — Demonstrates querying the view and verifying the returned data.

This demonstration closely matches the `ProductSummary` example in this article, showing how a keyless entity type can expose data from an existing view without requiring a primary key.

### Video 2 - Deep Dive into Many-to-Many: A Tour of EF Core 5.0 pt. 2

<iframe width="560" height="315" src="https://www.youtube.com/embed/b2klBzcALJc" title="Deep Dive into Many-to-Many: A Tour of EF Core 5.0 pt. 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In this official Microsoft video, Arthur Vickers and Jeremy Likness explore how EF Core 5 manages many-to-many relationships internally. The demonstration shows a dictionary-based shared-type join entity, explains how indexer properties represent its foreign keys, and illustrates how the join entity configuration can be customized.

**Key timestamps:**

- **[05:54](https://www.youtube.com/watch?v=b2klBzcALJc&t=354s)** — Shows how EF Core represents a many-to-many join entity using `Dictionary<string, object>`.
- **[06:38](https://www.youtube.com/watch?v=b2klBzcALJc&t=398s)** — Explains shared-type entity types and indexer properties.
- **[10:38](https://www.youtube.com/watch?v=b2klBzcALJc&t=638s)** — Introduces explicit configuration of a shared-type entity.
- **[16:17](https://www.youtube.com/watch?v=b2klBzcALJc&t=977s)** — Demonstrates customizing the join entity's name and foreign key properties.

Although the example focuses on many-to-many relationships in EF Core 5, it provides a practical connection between shared-type entities, indexer properties, and dictionary-based entity mapping. These concepts help explain how the specialized configurations presented in this article can work together.

## Summary

EF Core supports specialized entity types and property mappings for scenarios that do not fit conventional entity models.

Key points:

- **Keyless Entity Types:** Use `HasNoKey()` to map data without a primary key, such as database views and read-only query results.
- **Shared-Type Entity Types:** Use `SharedTypeEntity<T>()` to configure multiple entity types that share the same CLR type but have distinct names and mappings.
- **Indexer Properties:** Use `IndexerProperty<T>()` to map values accessed through a C# indexer rather than individual CLR properties.
- **Property Bag Entity Types:** Use `Dictionary<string, object>` with shared-type entity configuration to represent an entire entity through key-value pairs.

These features serve different purposes, but some work together. Property bag entity types, for example, combine named shared-type entities with indexer properties.

## Related Articles

Explore these related EF Core modeling features:

- [Shadow Properties in EF Core](/model/shadow-properties) — Learn how EF Core maps properties whose values are stored in the change tracker rather than the entity instance.
- [Many-to-Many Relationships in EF Core](/modeling/relationships-many-to-many) — Explore how EF Core configures join entities, including relationships that do not require an explicit join entity class.
- [Complex Types in EF Core](/modeling/complex-type) — Understand how to model structured values without giving them independent entity identity.

## FAQ

### Can indexer properties be used in LINQ queries?

Yes. You can reference a mapped indexer property using `EF.Property<T>()` or an indexer expression in a LINQ query, subject to the database provider's translation support.

### Can I add new dictionary keys to a property bag without changing the EF Core model?

You can add entries to a C# dictionary, but EF Core only maps properties defined in its model. To persist additional values as mapped columns, those properties must also be configured in the EF Core model.

### Can AsTracking() enable change tracking for keyless entity types?

No. Keyless entity types are never tracked for changes, even when `AsTracking()` is applied to a query. They cannot be updated through EF Core's normal change-tracking operations.

For tracking behavior in regular entity queries, see [Query Tracking in EF Core](/querying/query-tracking).