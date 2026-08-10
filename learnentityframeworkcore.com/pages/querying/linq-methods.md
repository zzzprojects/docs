---
title: LINQ Methods in EF Core
description: Learn how to use LINQ methods with EF Core to filter, sort, project, group, combine, and execute database queries.
canonical: /querying/linq-methods
status: Published
lastmod: 2026-08-10
---

# LINQ Methods in EF Core

This article covers the LINQ methods presented in the accompanying video and shows how to use them with EF Core to filter, sort, project, group, combine, and execute queries.

<iframe width="560" height="315" src="https://www.youtube.com/embed/tXOWx9OdMXE" title="¡Explicación de todos los métodos LINQ en .NET 10! (La guía completa)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The video demonstrates these methods primarily with `IEnumerable`. Most concepts also apply to EF Core `IQueryable` queries, but EF Core adds query translation and differences between database and in-memory execution.

The examples assume `Product` and `Category` entities exposed through `context.Products` and `context.Categories`. They use asynchronous EF Core methods when available.

> The examples target .NET 10 and EF Core 10. The `LeftJoin` and `RightJoin` operators require these versions. Earlier versions use the traditional `GroupJoin`, `SelectMany`, and `DefaultIfEmpty` pattern for left joins.

EF Core does not provide a dedicated reference for all LINQ methods. You can find the standard LINQ operator references [here](https://learn.microsoft.com/en-us/dotnet/csharp/linq/standard-query-operators/) and [here](https://learn.microsoft.com/en-us/dotnet/csharp/linq/get-started/introduction-to-linq-queries).

## LINQ Methods Video Timeline

Select a timestamp to view the corresponding method in the video.

| Method | Category | Timestamp |
|---|---|---:|
| [`Aggregate`](#aggregate) | Calculate Values | [01:15](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=75s) |
| [`All`](#all) | Check Conditions | [06:12](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=372s) |
| [`Any`](#any) | Check Conditions | [09:02](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=542s) |
| [`AsEnumerable`](#asenumerable) | Convert Sequences | [12:06](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=726s) |
| [`Average`](#average) | Calculate Values | [14:10](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=850s) |
| [`Cast`](#cast) | Convert Sequences | [15:27](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=927s) |
| [`Concat`](#concat) | Combine Sequences | [16:30](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=990s) |
| [`Contains`](#contains) | Check Conditions | [18:34](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1114s) |
| [`Count`](#count) | Calculate Values | [22:08](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1328s) |
| [`DefaultIfEmpty`](#defaultifempty) | Get Elements | [24:12](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1452s) |
| [`Distinct`](#distinct) | Set Operations and Sequence Comparison | [26:28](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1588s) |
| [`ElementAt`](#elementat) | Get Elements | [29:31](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1771s) |
| [`ElementAtOrDefault`](#elementatordefault) | Get Elements | [29:31](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1771s) |
| [`Empty`](#empty) | Create Sequences | [31:01](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1861s) |
| [`Except`](#except) | Set Operations and Sequence Comparison | [31:50](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1910s) |
| [`First`](#first) | Get Elements | [33:39](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2019s) |
| [`FirstOrDefault`](#firstordefault) | Get Elements | [33:39](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2019s) |
| [`GroupBy`](#groupby) | Group Data | [35:38](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2138s) |
| [`GroupJoin`](#groupjoin) | Join Data | [38:17](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2297s) |
| [`Intersect`](#intersect) | Set Operations and Sequence Comparison | [41:21](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2481s) |
| [`Join`](#join) | Join Data | [42:32](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2552s) |
| [`Last`](#last) | Get Elements | [48:12](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2892s) |
| [`LastOrDefault`](#lastordefault) | Get Elements | [48:12](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2892s) |
| [`LeftJoin`](#leftjoin) | Join Data | [44:48](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2688s) |
| [`LongCount`](#longcount) | Calculate Values | [50:08](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3008s) |
| [`Max`](#max) | Calculate Values | [51:10](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3070s) |
| [`Min`](#min) | Calculate Values | [51:10](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3070s) |
| [`OfType`](#oftype) | Filter Data | [52:16](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3136s) |
| [`OrderBy`](#orderby) | Sort Data | [53:19](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3199s) |
| [`OrderByDescending`](#orderbydescending) | Sort Data | [53:19](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3199s) |
| [`Range`](#range) | Create Sequences | [55:02](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3302s) |
| [`Repeat`](#repeat) | Create Sequences | [56:01](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3361s) |
| [`Reverse`](#reverse) | Sort Data | [57:13](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3433s) |
| [`RightJoin`](#rightjoin) | Join Data | [45:14](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2714s) |
| [`Select`](#select) | Select Data | [58:17](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3497s) |
| [`SelectMany`](#selectmany) | Select Data | [59:55](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3595s) |
| [`SequenceEqual`](#sequenceequal) | Set Operations and Sequence Comparison | [1:01:58](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3718s) |
| [`Single`](#single) | Get Elements | [1:03:52](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3832s) |
| [`SingleOrDefault`](#singleordefault) | Get Elements | [1:03:52](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3832s) |
| [`Skip`](#skip) | Page or Partition Results | [1:06:19](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3979s) |
| [`Take`](#take) | Page or Partition Results | [1:06:19](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3979s) |
| [`SkipWhile`](#skipwhile) | Page or Partition Results | [1:08:23](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4103s) |
| [`Sum`](#sum) | Calculate Values | [1:10:05](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4205s) |
| [`TakeWhile`](#takewhile) | Page or Partition Results | [1:10:57](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4257s) |
| [`ThenBy`](#thenby) | Sort Data | [1:12:26](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4346s) |
| [`ThenByDescending`](#thenbydescending) | Sort Data | [1:12:26](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4346s) |
| [`ToArray`](#toarray) | Convert Sequences | [1:13:13](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4393s) |
| [`ToDictionary`](#todictionary) | Convert Sequences | [1:13:53](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4433s) |
| [`ToList`](#tolist) | Convert Sequences | [1:14:43](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4483s) |
| [`ToLookup`](#tolookup) | Group Data | [1:15:26](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4526s) |
| [`Union`](#union) | Set Operations and Sequence Comparison | [1:16:36](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4596s) |
| [`Where`](#where) | Filter Data | [1:17:57](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4677s) |

## Check Conditions

Use these methods to check whether elements exist or satisfy a condition.

### `Any`

[`Any` in the video (09:02)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=542s)

`Any` checks whether a sequence contains at least one element or whether at least one element satisfies a condition.

With EF Core, use `AnyAsync()` to perform the existence check against the database:

```csharp
var hasOutOfStockProducts = await context.Products
    .AnyAsync(product => product.Stock == 0);
```

The result is `true` when at least one product has no stock. The matching products are not loaded.

### `All`

[`All` in the video (06:12)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=372s)

`All` checks whether every element in a sequence satisfies a condition.

The following query checks whether every product in a selected category has a `Price` of at least `10`:

```csharp
var categoryId = 1;

var allPricesAreAtLeastTen = await context.Products
    .Where(product => product.CategoryId == categoryId)
    .AllAsync(product => product.Price >= 10m);
```

The result is `true` only when every product returned by the filtered query satisfies the condition.

`AllAsync()` returns `true` for an empty sequence because no element fails the condition.

### `Contains`

[`Contains` in the video (18:34)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1114s)

`Contains` checks whether a sequence includes a specified value.

A common EF Core use is filtering rows against a local collection of values:

```csharp
var categoryIds = new[] { 1, 3 };

var products = await context.Products
    .Where(product => categoryIds.Contains(product.CategoryId))
    .ToListAsync();
```

This query returns products whose `CategoryId` is included in `categoryIds`.

When the source is an `IQueryable` of scalar values, `ContainsAsync()` can also execute the membership check as a terminal database operation.

## Calculate Values

Use these methods to count elements, calculate numeric values, or accumulate a custom result.

### `Count`

[`Count` in the video (22:08)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1328s)

`Count` returns the total number of elements in a sequence or the number that satisfy a condition.

With EF Core, use `CountAsync()` to perform the count in the database:

```csharp
var activeProductCount = await context.Products
    .CountAsync(product => product.IsActive);
```

The query returns the number of active products without loading the matching entities.

### `LongCount`

[`LongCount` in the video (50:08)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3008s)

`LongCount` works like `Count` but returns a `long` instead of an `int`.

Use `LongCountAsync()` when the result may exceed the range supported by `int`:

```csharp
var productCount = await context.Products.LongCountAsync();
```

The calculation is performed in the database, and the result is returned as a 64-bit integer.

### `Sum`

[`Sum` in the video (1:10:05)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4205s)

`Sum` calculates the total of a numeric value in a sequence.

The following query calculates the total stock for all active products:

```csharp
var totalStock = await context.Products
    .Where(product => product.IsActive)
    .SumAsync(product => product.Stock);
```

`SumAsync()` performs the calculation in the database without loading the matching products.

### `Average`

[`Average` in the video (14:10)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=850s)

`Average` calculates the average of a numeric value in a sequence.

The following query calculates the average product price:

```csharp
var averagePrice = await context.Products
    .AverageAsync(product => product.Price);
```

`AverageAsync()` returns a single numeric result without loading the products.

If a filtered query may be empty, project the value to a nullable numeric type when the application should receive `null`.

### `Min`

[`Min` in the video (51:10)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3070s)

`Min` returns the lowest value in a sequence.

The following query returns the lowest price among active products:

```csharp
var lowestActiveProductPrice = await context.Products
    .Where(product => product.IsActive)
    .MinAsync(product => product.Price);
```

If the filtered query may be empty, project `Price` to `decimal?` when the application should receive `null`.

### `Max`

[`Max` in the video (51:10)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3070s)

`Max` returns the highest value in a sequence.

The following query returns the highest price among active products:

```csharp
var highestActiveProductPrice = await context.Products
    .Where(product => product.IsActive)
    .MaxAsync(product => product.Price);
```

If the filtered query may be empty, project `Price` to `decimal?` when the application should receive `null`.

### `Aggregate`

[`Aggregate` in the video (01:15)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=75s)

`Aggregate` applies an accumulator function to each element and returns the final accumulated value.

Do not assume that EF Core can translate an arbitrary accumulator function into SQL. Load only the required values, and then apply `Aggregate` in memory:

```csharp
var stockValues = await context.Products
    .Where(product => product.IsActive)
    .Select(product => product.Stock)
    .ToListAsync();

var totalStock = stockValues.Aggregate(
    0,
    (total, stock) => total + stock);
```

EF Core executes the query when `ToListAsync()` is called. `Aggregate` then calculates `totalStock` from the materialized values in the application.

For a simple sum, use `SumAsync()` to perform the calculation in the database without loading the values first.

## Get Elements

Use these methods to retrieve an element by order, position, or expected result count, with or without a default result.

### `First`

[`First` in the video (33:39)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2019s)

`First` returns the first element in a sequence and throws an exception when the sequence is empty.

The following query defines a deterministic order before retrieving the first product:

```csharp
var firstProduct = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .FirstAsync();
```

### `FirstOrDefault`

[`FirstOrDefault` in the video (33:39)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2019s)

`FirstOrDefault` returns the first element in a sequence or the default value when the sequence is empty.

```csharp
var firstInactiveProduct = await context.Products
    .Where(product => !product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .FirstOrDefaultAsync();
```

For a reference type such as `Product`, the result is `null` when no row matches.

### `Last`

[`Last` in the video (48:12)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2892s)

`Last` returns the last element in a sequence and throws an exception when the sequence is empty.

The following query defines a deterministic order before retrieving the last product:

```csharp
var lastProduct = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .LastAsync();
```

### `LastOrDefault`

[`LastOrDefault` in the video (48:12)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2892s)

`LastOrDefault` returns the last element in a sequence or the default value when the sequence is empty.

```csharp
var lastInactiveProduct = await context.Products
    .Where(product => !product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .LastOrDefaultAsync();
```

The result is `null` when no inactive product matches.

### `Single`

[`Single` in the video (1:03:52)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3832s)

`Single` returns the only element in a sequence. It throws an exception when the sequence is empty or contains more than one element.

```csharp
var productId = 5;

var product = await context.Products
    .SingleAsync(product => product.ProductId == productId);
```

Use `SingleAsync()` when the query is expected to match exactly one row.

### `SingleOrDefault`

[`SingleOrDefault` in the video (1:03:52)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3832s)

`SingleOrDefault` returns the only element in a sequence or the default value when the sequence is empty. It throws an exception when more than one element matches.

```csharp
var productId = 5;

var product = await context.Products
    .SingleOrDefaultAsync(product => product.ProductId == productId);
```

The result is `null` when no product matches.

### `ElementAt`

[`ElementAt` in the video (29:31)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1771s)

`ElementAt` returns the element at a specified zero-based position and throws an exception when that position is outside the sequence.

Apply an explicit order before selecting an element by position:

```csharp
var thirdProduct = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ElementAtAsync(2);
```

The index `2` represents the third product. `ElementAtAsync()` uses the position in the ordered result, not the entity key.

### `ElementAtOrDefault`

[`ElementAtOrDefault` in the video (29:31)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1771s)

`ElementAtOrDefault` returns the element at a specified zero-based position or the default value when that position is outside the sequence.

```csharp
var tenthProduct = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ElementAtOrDefaultAsync(9);
```

For a reference type such as `Product`, the result is `null` when the ordered sequence contains fewer than ten elements.

### `DefaultIfEmpty`

[`DefaultIfEmpty` in the video (24:12)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1452s)

`DefaultIfEmpty` returns the original sequence when it contains elements. When the sequence is empty, it returns a sequence containing the default value for the element type or a specified fallback value.

The following example loads product names for a category and then applies `DefaultIfEmpty` in memory:

```csharp
var categoryId = 4;

var productNames = await context.Products
    .Where(product => product.CategoryId == categoryId)
    .Select(product => product.Name)
    .ToListAsync();

var namesOrDefault = productNames.DefaultIfEmpty("No products");
```

When `productNames` is empty, `namesOrDefault` contains one element with the value `"No products"`. Without an argument, the sequence contains the default value for its element type.

In EF Core queries, `DefaultIfEmpty()` is also used in the traditional LINQ pattern for a left join.

## Filter Data

Use these methods to keep only elements that match a condition or a specified type.

### `Where`

[`Where` in the video (1:17:57)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4677s)

`Where` filters a sequence by applying a condition to each element.

The following query returns active products that have stock available:

```csharp
var availableProducts = await context.Products
    .Where(product => product.IsActive && product.Stock > 0)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

`Where` adds the condition to the `IQueryable`, and only matching products are materialized when `ToListAsync()` executes the query.

Multiple `Where` calls can be composed before the query executes.

### `OfType`

[`OfType` in the video (52:16)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3136s)

`OfType<TResult>` keeps only elements assignable to the specified type.

In EF Core, it is commonly used with an inheritance hierarchy. The following example assumes that `DigitalProduct` derives from `Product` and is mapped in the EF Core model:

```csharp
var digitalProducts = await context.Products
    .OfType<DigitalProduct>()
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

The query returns only entities represented by the `DigitalProduct` type. EF Core translates the type filter according to the configured inheritance mapping and database provider.

Unlike `Cast<TResult>`, `OfType<TResult>` removes incompatible elements instead of requiring every element to be compatible.

## Select Data

Use these methods to shape query results or flatten related collections.

### `Select`

[`Select` in the video (58:17)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3497s)

`Select` projects each element into a new form.

The following query retrieves only the values required for a product summary:

```csharp
var productSummaries = await context.Products
    .Where(product => product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .Select(product => new
    {
        product.ProductId,
        product.Name,
        product.Price
    })
    .ToListAsync();
```

EF Core requests only the projected columns. Because the result contains scalar values rather than entities, no `Product` instances are created or tracked.

`Select` can project a single property, an anonymous type, or a custom result type.

### `SelectMany`

[`SelectMany` in the video (59:55)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3595s)

`SelectMany` projects each source element to a collection and flattens the resulting collections into one sequence.

The following example assumes that `Category` has a collection navigation named `Products`:

```csharp
var productsByCategory = await context.Categories
    .SelectMany(
        category => category.Products,
        (category, product) => new
        {
            CategoryName = category.Name,
            ProductName = product.Name
        })
    .OrderBy(item => item.CategoryName)
    .ThenBy(item => item.ProductName)
    .ToListAsync();
```

The result contains one row for each related product together with its category name. Categories with no products do not contribute an element to the flattened sequence.

The result-selector overload preserves values from both the source element and the collection element. When only the collection elements are needed, use the overload without a result selector.

## Sort Data

Use these methods to define the order of query results or reverse an existing sequence.

### `OrderBy`

[`OrderBy` in the video (53:19)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3199s)

`OrderBy` sorts a sequence in ascending order by a specified key.

The following query orders products by name:

```csharp
var products = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

`OrderBy` defines the primary sort key, while `ThenBy` provides a deterministic tie-breaker. The database applies both orderings when `ToListAsync()` executes the query.

Calling another `OrderBy` or `OrderByDescending` starts a new primary ordering. Use `ThenBy` or `ThenByDescending` to add another sort key.

### `OrderByDescending`

[`OrderByDescending` in the video (53:19)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3199s)

`OrderByDescending` sorts a sequence in descending order by a specified key.

The following query orders products from the highest price to the lowest:

```csharp
var products = await context.Products
    .OrderByDescending(product => product.Price)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

`ProductId` provides a deterministic order when multiple products have the same price.

Calling another `OrderBy` or `OrderByDescending` starts a new primary ordering. Use `ThenBy` or `ThenByDescending` to add another sort key.

### `ThenBy`

[`ThenBy` in the video (1:12:26)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4346s)

`ThenBy` adds an ascending secondary ordering to an already ordered sequence.

The following query orders products by category and then by name:

```csharp
var products = await context.Products
    .OrderBy(product => product.CategoryId)
    .ThenBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

Each `ThenBy` applies when elements have equal values for the preceding sort keys. The final `ProductId` ordering makes the result deterministic when products share the same category and name.

### `ThenByDescending`

[`ThenByDescending` in the video (1:12:26)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4346s)

`ThenByDescending` adds a descending secondary ordering to an already ordered sequence.

The following query places the most expensive products first within each category:

```csharp
var products = await context.Products
    .OrderBy(product => product.CategoryId)
    .ThenByDescending(product => product.Price)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

`CategoryId` remains the primary sort key. `ThenByDescending` affects only products in the same category.

### `Reverse`

[`Reverse` in the video (57:13)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3433s)

`Reverse` inverts the current order of a sequence.

The following example loads products in a defined order and then reverses the materialized list:

```csharp
var products = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();

products.Reverse();
```

`ToListAsync()` executes the ordered query, and `List<T>.Reverse()` then reverses the list in memory.

Without an explicit order, the result is not deterministic. Prefer `OrderByDescending` when the reverse order can be expressed directly in the database query.

## Group Data

Use these methods to organize elements by a key or create an in-memory lookup.

### `GroupBy`

[`GroupBy` in the video (35:38)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2138s)

`GroupBy` organizes elements into groups that share the same key.

The following query groups products by category and projects the grouping key with aggregate values:

```csharp
var categorySummaries = await context.Products
    .GroupBy(product => product.CategoryId)
    .Select(group => new
    {
        CategoryId = group.Key,
        ProductCount = group.Count(),
        MinimumPrice = group.Min(product => product.Price),
        MaximumPrice = group.Max(product => product.Price)
    })
    .OrderBy(summary => summary.CategoryId)
    .ToListAsync();
```

The result contains one row for each `CategoryId` present in the source sequence.

`group.Key` contains the value used to create each group. This query projects the grouping key and scalar aggregates rather than returning the groups themselves.

### `ToLookup`

[`ToLookup` in the video (1:15:26)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4526s)

`ToLookup` creates an `ILookup<TKey, TElement>` that associates each key with one or more elements.

`ToLookup` is a LINQ to Objects operation. Load only the required values before creating the lookup in memory:

```csharp
var productData = await context.Products
    .Select(product => new
    {
        product.CategoryId,
        product.Name
    })
    .ToListAsync();

var productsByCategory = productData.ToLookup(
    product => product.CategoryId,
    product => product.Name);

var categoryId = 1;
var productNames = productsByCategory[categoryId];
```

The lookup contains a group of product names for each `CategoryId`.

Unlike a dictionary, a lookup can associate multiple elements with the same key. When a key does not exist, it returns an empty sequence rather than throwing a key-not-found exception.

## Join Data

Use these methods to combine two sequences by matching their key values.

### `Join`

[`Join` in the video (42:32)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2552s)

`Join` combines elements from two sequences when their key values match.

The following query joins products with their categories:

```csharp
var productsWithCategories = await context.Products
    .Join(
        context.Categories,
        product => product.CategoryId,
        category => category.CategoryId,
        (product, category) => new
        {
            product.ProductId,
            category.CategoryId,
            ProductName = product.Name,
            CategoryName = category.Name
        })
    .OrderBy(item => item.CategoryName)
    .ThenBy(item => item.ProductName)
    .ThenBy(item => item.ProductId)
    .ToListAsync();
```

The key selectors match `Product.CategoryId` with `Category.CategoryId`, and the result selector defines the values returned for each matching pair.

With a relational database provider, this query shape is typically translated to an `INNER JOIN`. Elements without a matching key in the other sequence are not included.

### `GroupJoin`

[`GroupJoin` in the video (38:17)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2297s)

`GroupJoin` associates each element from the first sequence with all matching elements from the second sequence.

Because the result contains a nested collection, the following example loads only the required values and performs the `GroupJoin` in memory:

```csharp
var categoryData = await context.Categories
    .Select(category => new
    {
        category.CategoryId,
        category.Name
    })
    .ToListAsync();

var productData = await context.Products
    .Select(product => new
    {
        product.CategoryId,
        product.Name
    })
    .ToListAsync();

var categoriesWithProducts = categoryData
    .GroupJoin(
        productData,
        category => category.CategoryId,
        product => product.CategoryId,
        (category, products) => new
        {
            CategoryName = category.Name,
            ProductNames = products.Select(product => product.Name)
        })
    .ToList();
```

The result contains every category together with its matching product names. When a category has no matching products, `ProductNames` is empty.

`GroupJoin` can also be combined with `SelectMany` and `DefaultIfEmpty` to form the traditional left-join pattern.

### `LeftJoin`

[`LeftJoin` in the video (44:48)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2688s)

`LeftJoin` preserves every element from the first sequence and produces one result for each matching element from the second sequence.

The following query preserves every product and supplies category data when a matching category exists:

```csharp
var productsWithCategories = await context.Products
    .LeftJoin(
        context.Categories,
        product => product.CategoryId,
        category => category.CategoryId,
        (product, category) => new
        {
            product.ProductId,
            ProductName = product.Name,
            CategoryName = category == null
                ? "No category"
                : category.Name
        })
    .OrderBy(item => item.ProductName)
    .ThenBy(item => item.ProductId)
    .ToListAsync();
```

When no matching category exists, the category passed to the result selector is `null`.

### `RightJoin`

[`RightJoin` in the video (45:14)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2714s)

`RightJoin` preserves every element from the second sequence and produces one result for each matching element from the first sequence.

The following query preserves every category, including categories without products:

```csharp
var categoriesWithProducts = await context.Products
    .RightJoin(
        context.Categories,
        product => product.CategoryId,
        category => category.CategoryId,
        (product, category) => new
        {
            category.CategoryId,
            ProductId = product == null
                ? (int?)null
                : product.ProductId,
            CategoryName = category.Name,
            ProductName = product == null
                ? "No products"
                : product.Name
        })
    .OrderBy(item => item.CategoryName)
    .ThenBy(item => item.CategoryId)
    .ThenBy(item => item.ProductId)
    .ToListAsync();
```

When a category has no matching product, the product passed to the result selector is `null`.

## Page or Partition Results

Use these methods to select part of an ordered sequence by position or condition.

### `Skip`

[`Skip` in the video (1:06:19)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3979s)

`Skip` bypasses a specified number of elements and returns the remaining sequence.

The following query retrieves the third page of products when each page contains ten products:

```csharp
var pageNumber = 3;
var pageSize = 10;

var products = await context.Products
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .Skip((pageNumber - 1) * pageSize)
    .Take(pageSize)
    .ToListAsync();
```

The database omits the products from the preceding pages before returning the requested page.

Use a fully unique ordering before applying `Skip` and `Take` so that page boundaries remain deterministic.

### `Take`

[`Take` in the video (1:06:19)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3979s)

`Take` returns a specified number of elements from the beginning of a sequence.

The following query returns the ten most expensive products:

```csharp
var products = await context.Products
    .OrderByDescending(product => product.Price)
    .ThenBy(product => product.ProductId)
    .Take(10)
    .ToListAsync();
```

The ordering determines which products are returned. When fewer than ten products exist, `Take` returns all available elements.

### `SkipWhile`

[`SkipWhile` in the video (1:08:23)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4103s)

`SkipWhile` bypasses elements while a condition remains `true`. After the first element that does not satisfy the condition, it returns that element and all remaining elements.

The following example loads the required values in a defined order and then applies `SkipWhile` in memory:

```csharp
var productData = await context.Products
    .OrderBy(product => product.Price)
    .ThenBy(product => product.ProductId)
    .Select(product => new
    {
        product.ProductId,
        product.Name,
        product.Price
    })
    .ToListAsync();

var productsFromFifty = productData
    .SkipWhile(product => product.Price < 50m)
    .ToList();
```

The result starts with the first product whose price is at least `50`. Unlike `Where`, `SkipWhile` removes only the matching prefix of the ordered sequence.

### `TakeWhile`

[`TakeWhile` in the video (1:10:57)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4257s)

`TakeWhile` returns elements while a condition remains `true` and stops at the first element that does not satisfy it.

The following example loads the required values in a defined order and then applies `TakeWhile` in memory:

```csharp
var productData = await context.Products
    .OrderBy(product => product.Price)
    .ThenBy(product => product.ProductId)
    .Select(product => new
    {
        product.ProductId,
        product.Name,
        product.Price
    })
    .ToListAsync();

var productsBelowFifty = productData
    .TakeWhile(product => product.Price < 50m)
    .ToList();
```

The result contains the initial products whose price is below `50`. Unlike `Where`, `TakeWhile` returns only the matching prefix of the ordered sequence.

## Set Operations and Sequence Comparison

Use these methods to remove duplicates, compare sequences, or perform set operations.

### `Distinct`

[`Distinct` in the video (26:28)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1588s)

`Distinct` removes duplicate elements from a sequence.

The following query returns the unique category identifiers used by products:

```csharp
var categoryIds = await context.Products
    .Select(product => product.CategoryId)
    .Distinct()
    .OrderBy(categoryId => categoryId)
    .ToListAsync();
```

`Distinct` compares the projected `CategoryId` values as part of the database query, so each identifier appears only once. Because the result has no guaranteed order, apply `OrderBy` when a defined order is required.

### `Except`

[`Except` in the video (31:50)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1910s)

`Except` returns the distinct elements from the first sequence that do not appear in the second sequence.

The following query returns category identifiers used by products but not by any active product:

```csharp
var allProductCategoryIds = context.Products
    .Select(product => product.CategoryId);

var activeProductCategoryIds = context.Products
    .Where(product => product.IsActive)
    .Select(product => product.CategoryId);

var inactiveOnlyCategoryIds = await allProductCategoryIds
    .Except(activeProductCategoryIds)
    .OrderBy(categoryId => categoryId)
    .ToListAsync();
```

Each matching `CategoryId` appears only once.

`Except` is directional. Reversing the source sequences can produce a different result.

### `Intersect`

[`Intersect` in the video (41:21)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=2481s)

`Intersect` returns the distinct elements that appear in both sequences.

The following query returns category identifiers that contain both active products and out-of-stock products:

```csharp
var activeProductCategoryIds = context.Products
    .Where(product => product.IsActive)
    .Select(product => product.CategoryId);

var outOfStockProductCategoryIds = context.Products
    .Where(product => product.Stock == 0)
    .Select(product => product.CategoryId);

var matchingCategoryIds = await activeProductCategoryIds
    .Intersect(outOfStockProductCategoryIds)
    .OrderBy(categoryId => categoryId)
    .ToListAsync();
```

Only identifiers present in both sequences are returned, and duplicate values are removed.

### `SequenceEqual`

[`SequenceEqual` in the video (1:01:58)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3718s)

`SequenceEqual` determines whether two sequences contain the same number of elements with equal values in the same order.

The following example loads ordered product identifiers from two categories and compares the resulting sequences:

```csharp
var firstCategoryId = 1;
var secondCategoryId = 2;

var firstProductIds = await context.Products
    .Where(product => product.CategoryId == firstCategoryId)
    .OrderBy(product => product.ProductId)
    .Select(product => product.ProductId)
    .ToListAsync();

var secondProductIds = await context.Products
    .Where(product => product.CategoryId == secondCategoryId)
    .OrderBy(product => product.ProductId)
    .Select(product => product.ProductId)
    .ToListAsync();

var productIdsAreEqual = firstProductIds
    .SequenceEqual(secondProductIds);
```

After both queries are materialized, `SequenceEqual` compares the lists in memory.

The sequences must contain the same values in the same order. Use an explicit ordering when database results are compared by position.

### `Union`

[`Union` in the video (1:16:36)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4596s)

`Union` returns the distinct elements that appear in either of two sequences.

The following query returns category identifiers associated with active or out-of-stock products:

```csharp
var activeProductCategoryIds = context.Products
    .Where(product => product.IsActive)
    .Select(product => product.CategoryId);

var outOfStockProductCategoryIds = context.Products
    .Where(product => product.Stock == 0)
    .Select(product => product.CategoryId);

var categoryIds = await activeProductCategoryIds
    .Union(outOfStockProductCategoryIds)
    .OrderBy(categoryId => categoryId)
    .ToListAsync();
```

Each category identifier appears once, even when it occurs in both source sequences.

## Combine Sequences

Use this method to append one sequence to another while preserving duplicate values.

### `Concat`

[`Concat` in the video (16:30)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=990s)

`Concat` appends the elements from the second sequence after the elements from the first sequence.

The following example loads active and inactive product names separately and then combines them:

```csharp
var activeProductNames = await context.Products
    .Where(product => product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .Select(product => product.Name)
    .ToListAsync();

var inactiveProductNames = await context.Products
    .Where(product => !product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .Select(product => product.Name)
    .ToListAsync();

var productNames = activeProductNames
    .Concat(inactiveProductNames)
    .ToList();
```

After both queries are materialized, `Concat` appends the inactive product names to the active product names in memory.

Unlike `Union`, `Concat` preserves duplicate values.

## Create Sequences

Use these methods to define sequences in the application without querying the database.

### `Empty`

[`Empty` in the video (31:01)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=1861s)

`Enumerable.Empty<TResult>()` returns an empty sequence of the specified type.

The following example creates an empty sequence of product names:

```csharp
var productNames = Enumerable.Empty<string>();
```

`Empty` is useful when a method must return an `IEnumerable<T>` but has no elements to provide.

### `Range`

[`Range` in the video (55:02)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3302s)

`Range` creates a sequence of consecutive integers.

The following example creates page numbers from `1` through `5`:

```csharp
var pageNumbers = Enumerable.Range(1, 5);
```

The first argument specifies the starting value, and the second specifies how many integers to generate. The resulting sequence contains `1`, `2`, `3`, `4`, and `5`.

### `Repeat`

[`Repeat` in the video (56:01)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=3361s)

`Repeat` creates a sequence containing the same value a specified number of times.

The following example creates the same label three times:

```csharp
var placeholders = Enumerable.Repeat("Not available", 3);
```

The first argument specifies the value, and the second specifies the number of elements to create.

## Convert Sequences

Use these methods to change how a sequence is processed, convert its element type, or materialize its results in a collection.

### `AsEnumerable`

[`AsEnumerable` in the video (12:06)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=726s)

`AsEnumerable` exposes an `IQueryable<T>` as an `IEnumerable<T>` so that subsequent operators use LINQ to Objects.

The following example applies a database filter before switching to client-side processing:

```csharp
static bool UsesCustomClientLogic(Product product)
{
    return product.Name.Length <= 20;
}

var productNames = context.Products
    .Where(product => product.IsActive)
    .AsEnumerable()
    .Where(product => UsesCustomClientLogic(product))
    .Select(product => product.Name);
```

The first `Where` remains part of the EF Core query. After `AsEnumerable()`, the second `Where` and `Select` run in the application when the sequence is enumerated.

`AsEnumerable()` does not execute or buffer the query by itself. It changes which LINQ implementation is used by subsequent operators.

### `Cast`

[`Cast` in the video (15:27)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=927s)

`Cast<TResult>` casts every element in a sequence to the specified type.

The following example loads products, exposes them through the non-generic `IEnumerable` interface, and casts them back to `Product`:

```csharp
var products = await context.Products
    .OrderBy(product => product.Name)
    .ToListAsync();

System.Collections.IEnumerable values = products;

var typedProducts = values.Cast<Product>();
```

The database query executes before the cast. `Cast<Product>()` then attempts to cast each in-memory element to `Product` when the sequence is enumerated.

Every element must be compatible with `Product`; otherwise, enumeration throws an `InvalidCastException`. Unlike `OfType<Product>()`, `Cast<Product>()` does not remove incompatible elements.

### `ToArray`

[`ToArray` in the video (1:13:13)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4393s)

`ToArray` materializes a sequence as an array.

The following query materializes active product names in a `string[]`:

```csharp
var productNames = await context.Products
    .Where(product => product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .Select(product => product.Name)
    .ToArrayAsync();
```

Use an explicit ordering when the position of elements in the resulting array matters.

### `ToDictionary`

[`ToDictionary` in the video (1:13:53)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4433s)

`ToDictionary` materializes a sequence into a dictionary by selecting a unique key for each element.

The following query associates each product identifier with its name:

```csharp
var productsById = await context.Products
    .Select(product => new
    {
        product.ProductId,
        product.Name
    })
    .ToDictionaryAsync(
        product => product.ProductId,
        product => product.Name);
```

`ToDictionaryAsync()` returns a `Dictionary<int, string>`.

Duplicate keys cause `ToDictionaryAsync()` to throw an `ArgumentException`.

### `ToList`

[`ToList` in the video (1:14:43)](https://www.youtube.com/watch?v=tXOWx9OdMXE&t=4483s)

`ToList` materializes the elements of a sequence into a `List<T>`.

The following query materializes active `Product` entities in a list:

```csharp
var products = await context.Products
    .Where(product => product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

The returned entities are tracked by the current `DbContext` unless the query uses `AsNoTracking()`.

Subsequent LINQ operations on `products` run in memory.