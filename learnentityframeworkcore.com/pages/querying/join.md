---
title: Join in EF Core
description: Learn how to use Join in EF Core, including inner, left, right, group, and composite-key joins for related and unrelated query sources.
canonical: /querying/join
status: Published
lastmod: 2026-08-18
---

# Join in EF Core

A join in EF Core combines two query sources by matching their keys and can project values from both sources into a new result shape. You can use joins with entities that have a configured relationship or with sources that simply share compatible key values.

## Inner Join

Use [`Join`](/querying/linq-methods#join) when you only want results whose keys match in both query sources.

```csharp
var productsWithCategories = await context.Products
    .Join(
        context.Categories,
        product => product.CategoryId,
        category => category.CategoryId,
        (product, category) => new
        {
            ProductName = product.Name,
            CategoryName = category.Name
        })
    .ToListAsync();
```

This query joins `Products` and `Categories` by comparing `Product.CategoryId` with `Category.CategoryId`.

The result selector creates an anonymous type with two properties:

* `ProductName`
* `CategoryName`

The application therefore receives a list of projected objects with those two values rather than complete `Product` and `Category` entities. See [Projection](/querying/projection) for more about shaping query results.

Because this is an inner join, only products whose `CategoryId` matches a category are returned. With a relational database provider, this query shape is typically translated to an SQL `INNER JOIN`.

`Join()` composes the query; it does not execute it. The result remains an `IQueryable` until `ToListAsync()` executes the query and materializes the results. See [LINQ Queries](/querying/linq-queries) for more about query composition and execution.

## Left Join

Use [`LeftJoin`](/querying/linq-methods#leftjoin) when you want to keep every row from the first query source, even when there is no matching row in the second source.

```csharp
var categoriesWithProducts = await context.Categories
    .LeftJoin(
        context.Products,
        category => category.CategoryId,
        product => product.CategoryId,
        (category, product) => new
        {
            CategoryName = category.Name,
            ProductName = product == null ? null : product.Name
        })
    .ToListAsync();
```

This query keeps every `Category` because `Categories` is the first query source. When a category has a matching product, `ProductName` contains the product name. When there is no match, the product side is optional and `ProductName` is `null`.

The result is a list of anonymous objects containing `CategoryName` and a nullable `ProductName`. For example, a `Books` category with no matching product still appears in the result with `ProductName` set to `null`.

[`LeftJoin` is available in .NET 10](/efcore/efcore-10-what-is-new#new-linq-methods-leftjoin-and-rightjoin), and EF Core 10 can translate it to a relational `LEFT JOIN` when supported by the provider. In earlier .NET versions, left joins were commonly expressed using the `GroupJoin` + `SelectMany` + `DefaultIfEmpty` pattern.

`LeftJoin` preserves the first query source.

## Right Join

Use [`RightJoin`](/querying/linq-methods#rightjoin) when you want to keep every row from the second query source, even when there is no matching row in the first source.

```csharp
var categoriesWithProducts = await context.Products
    .RightJoin(
        context.Categories,
        product => product.CategoryId,
        category => category.CategoryId,
        (product, category) => new
        {
            CategoryName = category.Name,
            ProductName = product == null ? null : product.Name
        })
    .ToListAsync();
```

This time, `Categories` is the second query source, so every category remains in the result. If no product matches a category, `ProductName` is `null`.

The result has the same shape as the left join example: an anonymous object containing `CategoryName` and a nullable `ProductName`.

[`RightJoin` has the same .NET 10](/efcore/efcore-10-what-is-new#new-linq-methods-leftjoin-and-rightjoin) and EF Core 10 version requirements as `LeftJoin`, and EF Core can translate it to a relational `RIGHT JOIN` when supported by the provider.

The key difference between the two operators is which source is preserved:

* `LeftJoin` keeps every row from the first query source.
* `RightJoin` keeps every row from the second query source.

## Group Join

Use [`GroupJoin`](/querying/linq-methods#groupjoin) when you want each element from the first sequence together with a group containing all matching elements from the second sequence.

Unlike the previous join examples, a grouped result contains a collection for each outer element. A `GroupJoin` that returns an outer element together with a collection of matching inner elements does not translate directly to the server in many cases.

For this collection-shaped result, a clear approach is to materialize the required data first and then perform the `GroupJoin` in memory:

```csharp
var categories = await context.Categories
    .Select(category => new
    {
        category.CategoryId,
        category.Name
    })
    .ToListAsync();

var products = await context.Products
    .Select(product => new
    {
        product.CategoryId,
        product.Name
    })
    .ToListAsync();

var categoriesWithProducts = categories
    .GroupJoin(
        products,
        category => category.CategoryId,
        product => product.CategoryId,
        (category, matchingProducts) => new
        {
            CategoryName = category.Name,
            ProductNames = matchingProducts
                .Select(product => product.Name)
                .ToList()
        })
    .ToList();
```

The two `ToListAsync()` calls execute the EF Core queries and materialize the selected category and product values. After materialization, `categories` and `products` are in-memory lists rather than `IQueryable` sources, so `GroupJoin` runs over them using LINQ to Objects.

The result is a list of anonymous objects containing:

* `CategoryName`
* `ProductNames`, a `List<string>` with the matching product names

For example, a `Beverages` category can contain `Coffee` and `Tea`, while a `Books` category with no matching products still appears with an empty `ProductNames` list.

This is different from SQL `GROUP BY`, which groups rows by keys for aggregation rather than attaching a collection of matches to each outer element.

## Join on Composite Keys

Use `Join` with composite keys when matching rows requires more than one property.

In this example, `InventoryEntry` and `PriceEntry` are matched by both `ProductId` and `StoreId`:

```csharp
var inventoryWithPrices = await context.InventoryEntries
    .Join(
        context.PriceEntries,
        inventory => new
        {
            inventory.ProductId,
            inventory.StoreId
        },
        price => new
        {
            price.ProductId,
            price.StoreId
        },
        (inventory, price) => new
        {
            inventory.ProductId,
            inventory.StoreId,
            inventory.Quantity,
            price.Price
        })
    .ToListAsync();
```

Each key selector creates an anonymous object with the same two components:

* `ProductId`
* `StoreId`

The anonymous key objects must have matching property names and corresponding compatible types so both key selectors produce the same key shape. These properties do not need to form the primary key of either entity; they are simply the values used by this join.

A match is produced only when both values match. For this anonymous-key pattern, EF Core translates the join condition by comparing the corresponding key components.

The result is a list of anonymous objects containing:

* `ProductId`
* `StoreId`
* `Quantity`
* `Price`

`InventoryEntry` and `PriceEntry` do not need a configured EF Core relationship for this join. The join is based on the key selectors in the query, not on a navigation property.

## Join vs Include

`Join` and [`Include`](/querying/include) solve different problems.

Use `Join` when you want to combine query sources by matching keys and shape the result with values from both sources. The examples above return anonymous objects rather than loading complete related entities into navigation properties.

Use `Include` when you want to load related entities into the navigation properties of the root entities returned by the query.

In short:

* `Join` combines sources and creates the result shape you select.
* `Include` loads related entities through configured navigation properties.

## External Resources - Join

The following videos are useful if you want to see `LeftJoin`, `RightJoin`, and `GroupJoin` in practical examples. The first focuses on the new `LeftJoin` and `RightJoin` operators in .NET 10 with EF Core 10. The second adds a practical comparison with navigation-property queries, while the third helps illustrate the grouped result shape produced by LINQ `GroupJoin`.

### Video 1 - EF Core 10 Finally Adds LeftJoin + RightJoin (Too Little, Too Late?)

<iframe width="560" height="315" src="https://www.youtube.com/embed/vhshkKRHE9o" title="EF Core 10 Finally Adds LeftJoin + RightJoin (Too Little, Too Late?)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović compares the historical left-join pattern with the direct `LeftJoin` and `RightJoin` operators available with .NET 10 and supported by EF Core 10. The video also demonstrates the resulting query behavior and inspects the SQL sent to the database.

**Key timestamps:**

* [0:18](https://www.youtube.com/watch?v=vhshkKRHE9o&t=18s) — Visual explanation of how a left join preserves the left source and handles unmatched rows
* [6:20](https://www.youtube.com/watch?v=vhshkKRHE9o&t=380s) — Building a query with the direct `LeftJoin` method
* [7:27](https://www.youtube.com/watch?v=vhshkKRHE9o&t=447s) — Method syntax and the lack of a direct query-syntax form for the new operator
* [8:20](https://www.youtube.com/watch?v=vhshkKRHE9o&t=500s) — Implementing the corresponding `RightJoin`

### Video 2 - EF Core just fixed one of its biggest limitations

<iframe width="560" height="315" src="https://www.youtube.com/embed/tyRXfvo1Qvk" title="EF Core just fixed one of its biggest limitations" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Round The Code demonstrates practical `LeftJoin` and `RightJoin` queries in .NET 10, including DTO projections and a comparison with navigation-property queries. It also shows how query syntax still relies on the historical outer-join pattern.

**Key timestamps:**

* [1:11](https://www.youtube.com/watch?v=tyRXfvo1Qvk&t=71s) — Using `Include` for a simple related-data query before moving to explicit joins
* [4:51](https://www.youtube.com/watch?v=tyRXfvo1Qvk&t=291s) — Implementing the query with the direct `LeftJoin` method
* [5:58](https://www.youtube.com/watch?v=tyRXfvo1Qvk&t=358s) — Building a `RightJoin` query and projecting the result
* [6:30](https://www.youtube.com/watch?v=tyRXfvo1Qvk&t=390s) — Using the historical `join ... into` and `DefaultIfEmpty()` pattern with query syntax

### Video 3 - LINQ GroupJoin Explained in C# | Master join into in .NET 10

<iframe width="560" height="315" src="https://www.youtube.com/embed/kw-YRVRt8cE" title="LINQ GroupJoin Explained in C# | Master join into in .NET 10" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Harshit Agarwal provides a focused introduction to LINQ `GroupJoin` and its hierarchical result shape. The examples use in-memory collections rather than EF Core, making this video most useful for understanding how each outer element is paired with a collection of matching inner elements.

**Key timestamps:**

* [0:07](https://www.youtube.com/watch?v=kw-YRVRt8cE&t=7s) — Understanding `GroupJoin` and hierarchical results
* [2:20](https://www.youtube.com/watch?v=kw-YRVRt8cE&t=140s) — Using `join ... into` to create and project a grouped result
* [4:25](https://www.youtube.com/watch?v=kw-YRVRt8cE&t=265s) — Showing an outer element with an empty group when no matches exist
* [5:30](https://www.youtube.com/watch?v=kw-YRVRt8cE&t=330s) — Implementing the same grouped result with method-syntax `GroupJoin`

## Summary

EF Core can combine related or unrelated query sources by matching compatible key values.

* Use `Join` when you only want rows with matching keys.
* Use `LeftJoin` to keep every row from the first query source.
* Use `RightJoin` to keep every row from the second query source.
* Use `GroupJoin` when each outer element needs a collection of matching inner elements.
* Use anonymous key objects when a join must match multiple properties.

`Join`, `LeftJoin`, and `RightJoin` compose an `IQueryable` and are executed when a terminal operation such as `ToListAsync()` materializes the results. For collection-shaped `GroupJoin` results, translation is more limited, so the example in this article materializes the required values before performing the grouping in memory.

## Related Articles

* [LINQ Methods](/querying/linq-methods) — Learn how to use individual LINQ operators with EF Core.
* [LINQ Queries](/querying/linq-queries) — Learn how to build, compose, and execute EF Core queries.
* [Include](/querying/include) — Learn how to load related entities through navigation properties.
* [Projection](/querying/projection) — Learn how to shape query results with only the data you need.

## FAQ

### Can I join entities that do not have a relationship in EF Core?

Yes. `Join` does not require a configured navigation property or EF Core relationship. The query can join two sources as long as the key selectors provide compatible values to compare.

### What is the difference between `Join` and `Include`?

`Join` combines query sources by matching keys and lets you shape a new result from values on both sides.

[`Include`](/querying/include) loads related entities into navigation properties of the root entities returned by the query.

### Does `Join()` execute the query immediately?

No. When used with EF Core `IQueryable` sources, `Join()` composes the query. A terminal operation such as `ToListAsync()` executes it and materializes the results.

### What versions support `LeftJoin` and `RightJoin`?

The direct `LeftJoin` and `RightJoin` LINQ operators were [introduced in .NET 10](/efcore/efcore-10-what-is-new#new-linq-methods-leftjoin-and-rightjoin), and EF Core 10 can translate these operators for relational providers that support the required SQL. Earlier versions use other LINQ patterns to express outer joins.

### Does `GroupJoin` translate to SQL?

Not in every result shape. A `GroupJoin` that returns each outer element together with a collection of matching inner elements does not translate directly to the server in many cases. In the example in this article, the required data is materialized first and the `GroupJoin` is then performed in memory.