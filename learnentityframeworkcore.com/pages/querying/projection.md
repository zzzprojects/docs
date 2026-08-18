---
title: Projection in EF Core
description: Learn how to use Select in EF Core to project only the data you need into properties, anonymous types, DTOs, counts, and nested results.
canonical: /querying/projection
status: Published
lastmod: 2026-08-17
---

# Projection in EF Core

Entity Framework Core uses [`Select`](/querying/linq-methods#select) to shape query results and return only the data an application needs. A projection can return a single property, multiple properties, or a custom result shape, so the query does not have to materialize the complete entity when it is not needed.

## Select a Single Property

Use `Select` to return one property from each matching entity.

The following query returns only product names:

```csharp
var productNames = await context.Products
    .Select(product => product.Name)
    .ToListAsync();
```

`Select(product => product.Name)` changes the query from returning `Product` entities to returning `string` values. After `ToListAsync()`, the result is a `List<string>`.

Because the result only requires `Name`, a relational provider can request that column instead of retrieving every column needed to materialize a complete `Product` entity.

## Select Multiple Properties

Use an anonymous type inside `Select` when the result needs several properties instead of the complete entity.

```csharp
var products = await context.Products
    .Select(product => new
    {
        product.ProductId,
        product.Name,
        product.Price
    })
    .ToListAsync();
```

This projection returns `ProductId`, `Name`, and `Price` for each product.

After `ToListAsync()`, the result is a list of anonymous objects containing `ProductId`, `Name`, and `Price`.

EF Core does not materialize `Product` entities for this result. Instead, each result contains only the values defined by the projection.

The `new { ... }` expression creates an anonymous type. You can also customize the member names of that projected result.

## Project into an Anonymous Type

An anonymous type can define its own member names instead of keeping the property names from the entity.

```csharp
var products = await context.Products
    .Select(product => new
    {
        Id = product.ProductId,
        ProductName = product.Name,
        UnitPrice = product.Price
    })
    .ToListAsync();
```

The result contains the same underlying values, but exposes them through the members `Id`, `ProductName`, and `UnitPrice`.

Anonymous types are useful for ad hoc projections when the result does not need a reusable named type. When the projected shape needs a reusable named type outside the immediate query, use a DTO.

## Project into a DTO

Define a DTO for the projected result:

```csharp
public class ProductDto
{
    public int ProductId { get; set; }

    public string Name { get; set; } = null!;

    public decimal Price { get; set; }
}
```

Then project the required values directly into that type:

```csharp
var products = await context.Products
    .Select(product => new ProductDto
    {
        ProductId = product.ProductId,
        Name = product.Name,
        Price = product.Price
    })
    .ToListAsync();
```

After `ToListAsync()`, the query returns a `List<ProductDto>` instead of a list of `Product` entities.

`ProductDto` does not need to be exposed as a `DbSet` or configured as an entity in the EF Core model. It is simply the type used to shape the query result.

## Project with a Count

A projection can also include a calculated value based on related data.

The following query returns each category name together with the number of related products:

```csharp
var categories = await context.Categories
    .Select(category => new
    {
        category.Name,
        ProductCount = category.Products.Count()
    })
    .ToListAsync();
```

When the provider can translate the expression, EF Core can calculate this related count in the database without materializing the `Product` entities just to count them.

A category with no related products is still returned with a `ProductCount` of `0`.

This pattern is useful when the result needs summary values such as counts without loading the related entities themselves.

## Project Nested Data

A projection can also shape related data into a nested result.

The following query returns each category together with the names and prices of its related products:

```csharp
var categories = await context.Categories
    .Select(category => new
    {
        category.Name,
        Products = category.Products
            .OrderBy(product => product.ProductId)
            .Select(product => new
            {
                product.Name,
                product.Price
            })
            .ToList()
    })
    .ToListAsync();
```

The outer projection returns the category name, while the nested `Select` defines the shape of each related product.

Only `Name` and `Price` are included in the nested product results. The query does not materialize complete `Category` or `Product` entities for this projection.

A category with no related products is still returned, with an empty `Products` collection.

In this pattern, you do not need to call [`Include`](/querying/include) when the result only needs projected values from a related navigation. Use `Include` when the query needs to load related entities into their navigation properties instead.

## How Projection Affects the Query

`Select` changes the shape of an EF Core query, but it does not execute the query by itself.

For example:

```csharp
var query = context.Products
    .Select(product => product.Name);

var productNames = await query.ToListAsync();
```

After `Select`, the query remains an `IQueryable<string>` that can be composed further. `ToListAsync()` executes the query and materializes the projected values.

This is the same deferred-execution behavior used by other LINQ operators in EF Core.

## Projection and Tracking

Projection does not automatically mean that tracking is disabled. Tracking depends on whether entity instances are included in the result.

A projection that contains only scalar values does not contain entity instances to track:

```csharp
var products = await context.Products
    .Select(product => new
    {
        product.ProductId,
        product.Name
    })
    .ToListAsync();
```

However, if the projection contains an entity instance, EF Core tracks that entity by default in a tracking query:

```csharp
var results = await context.Products
    .Select(product => new
    {
        Product = product,
        CategoryName = product.Category.Name
    })
    .ToListAsync();
```

In this example, the anonymous result itself is not an entity, but the `Product` instance inside it is tracked.

## Projection and Query Translation

EF Core sends the LINQ query to the database provider, which translates supported expressions into the database query language.

EF Core can translate many common projections, including mapped properties and aggregates, when the database provider supports the complete query expression.

When a projection selects only the values it needs, a relational provider can generate a query that retrieves only the required columns. The exact SQL depends on the database provider and query shape.

Do not assume that every C# expression inside a projection can be translated to SQL. Translation support can vary by expression, provider, and EF Core version.

EF Core can evaluate part of the top-level projection — the final `Select` — in the application when that part cannot be translated, after retrieving the required data from the database. Expressions that cannot be translated in other parts of the query can cause a runtime exception.

## External Resources - Projection

The following videos are useful if you want to see `Select` projections in practical Entity Framework Core examples. The first demonstrates projecting related data directly into a DTO, including a calculated count. The second visually compares the SQL generated when retrieving a complete entity with the SQL generated after projecting only the required columns.

### Video 1 - Selecting columns with SELECT and using DTOs in Entity Framework Core (Step-by-step tutorial)

<iframe width="560" height="315" src="https://www.youtube.com/embed/r0-gZFTJ9Ew" title="Selecting columns with SELECT and using DTOs in Entity Framework Core (Step-by-step tutorial)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

render2web demonstrates how to create a DTO projection with `Select`, access a related category value, and calculate the number of related comments with `Count()`. The final query is materialized asynchronously with `ToListAsync()`, and the resulting projected data is displayed in the application.

The exact .NET and EF Core versions and database provider are not identified in the video, so it is best used as a practical demonstration of the projection pattern rather than as a version-specific EF Core reference.

**Key timestamps:**

* [4:24](https://www.youtube.com/watch?v=r0-gZFTJ9Ew&t=264s) — Introducing `Select` and DTO projection
* [7:28](https://www.youtube.com/watch?v=r0-gZFTJ9Ew&t=448s) — Building the projection into `ArticuloResumenDTO`
* [7:42](https://www.youtube.com/watch?v=r0-gZFTJ9Ew&t=462s) — Adding `Comentarios.Count()` as a calculated value inside the projection
* [15:51](https://www.youtube.com/watch?v=r0-gZFTJ9Ew&t=951s) — Showing the final projected result with title, category, and total comments

### Video 2 - Don't SUCK With Entity Framework - Select * - Performance Tips Part 3

<iframe width="560" height="315" src="https://www.youtube.com/embed/zsR_9RmsFIo" title="Don't SUCK With Entity Framework - Select * - Performance Tips Part 3" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Code It Up by AMBITIONED compares a query that retrieves the complete `Cat` entity with one that uses `Select` to project only `Name` and `Age`. SQL Server Profiler is used to show the difference in the generated SQL, making the effect of projection on the selected columns directly visible.

The project targets .NET Core 3.0 and uses SQL Server. Although the video predates EF Core 10, the selected segments demonstrate the same core projection pattern used in current EF Core: shape the result with `Select` when the application needs only specific values.

**Key timestamps:**

* [2:57](https://www.youtube.com/watch?v=zsR_9RmsFIo&t=177s) — SQL Server Profiler shows the query retrieving all mapped columns for the entity
* [6:57](https://www.youtube.com/watch?v=zsR_9RmsFIo&t=417s) — Using `Select` to project only `Name` and `Age` into an anonymous result
* [7:09](https://www.youtube.com/watch?v=zsR_9RmsFIo&t=429s) — SQL Server Profiler shows the projected query retrieving only `Name` and `Age`
* [13:16](https://www.youtube.com/watch?v=zsR_9RmsFIo&t=796s) — Projecting `Name` and `Age` into a named `CatResult` type with `Select`

## Summary

Use `Select` to shape an EF Core query so that it returns only the data the application needs.

A projection can return scalar values, multiple properties, anonymous types, DTOs, aggregates such as related counts, or nested related data. `Select` composes the query, while a terminal method such as `ToListAsync()` executes it and materializes the projected result.

Projection can reduce the data retrieved when complete entity instances are not required. If a projection contains an entity instance, that entity can still be tracked. Query translation depends on the expression, database provider, and EF Core version.

## Related Articles

* [LINQ Queries](/querying/linq-queries) — Learn how to build, compose, and execute EF Core queries.
* [LINQ Methods](/querying/linq-methods) — Learn how to use `Select` and other LINQ operators with EF Core.
* [Include](/querying/include) — Learn how to load related entities into navigation properties.

## FAQ

### Does projection automatically disable tracking?

No. A projection that returns only scalar values, or anonymous types or DTOs that contain no entity instances, has no entity instances to track. If the projected result contains an entity instance, EF Core tracks that entity by default in a tracking query.

### Do I need `Include` when projecting related data?

No. If the result only needs selected values from a related navigation, those values can be projected directly with `Select`. Use `Include` when the query needs related entities loaded into navigation properties.

### Can I project directly into a DTO?

Yes. You can create a DTO directly inside `Select` without exposing that DTO as a `DbSet` or configuring it as an entity in the EF Core model.

### Does EF Core translate every expression inside `Select` to SQL?

No. Translation depends on the expression, database provider, and EF Core version. EF Core can evaluate some non-translatable expressions in the top-level projection on the client, while non-translatable expressions elsewhere in the query can cause a runtime exception.