---
Title: EF Core 8 Bulk Insert Optimized | Improve your Insert Performance
MetaDescription: Boost EF Core insert performance with BulkInsertOptimized. Easily insert large numbers of entities without outputting values for the best performance. Get hints and recommendations about what could be improved to improve insert performance - try it now.
LastMod: 2023-11-16
---

# EF Core Bulk Insert Optimized

The method `BulkInsertOptimized` is a new method introduced to our library for [EF Core 8](/v8-101-0-0-efcore-8) and below. You can use it exactly like you do with the [BulkInsert](/bulk-insert) method:

```csharp
context.BulkInsertOptimized(list);
```

The main difference between this method and the [BulkInsert](/bulk-insert) method is the `BulkInsertOptimized` method doesn't output value by default. In other words, it will not automatically return and set the inserted identity value or any other values after the insertion.

A key advantage of not outputting values by default is to be able to use the `BulkCopy` strategy directly into the destination table instead of creating a temporary table (when outputting values).

Another key benefit of the `BulkInsertOptimized` method is its ability to suggest improvements for optimal performance, ensuring the use of `BulkCopy` into the destination table.

## What is the difference between BulkInsert and BulkInsertOptimized?

The [BulkInsert](/bulk-insert) output identity values (or any other values) by default, while the `BulkInsertOptimized` doesn't output any values by default.

In short, the `BulkInsertOptimized` is similar to doing a `BulkInsert` with the `AutoMapOutputDirection = false` option.

```csharp
context.BulkInsert(customers, options => options.AutoMapOutputDirection = false); 
context.BulkInsertOptimized(customers);
```

Another difference is that `BulkInsertOptimized` provides hints and recommendations for better performance whenever something has been found.

## What are the performance gains when not outputting values

Below is a benchmark showing the performance for a list of `Customer` with the `CustomerID` as an identity value:

| Operations      		| 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------------- | -------------: | -------------: | -------------: |
| BulkInsert     		| 220 ms       	 | 330 ms         | 600 ms         |
| BulkInsertOptimized   | 100 ms         | 150 ms         | 250 ms         |

[Try it](https://dotnetfiddle.net/RfxOjO)

Both are extremely fast, but the `BulkInsertOptimized` will always be faster due to not having to output values.

## Recommendations and Performance Hints

That feature is not yet completed but is coming soon.

However, some recommendations you will be able to find will be:

- Why a `BulkCopy` into the destination table could not have been done
- Why you should lock the table if you can
- etc.

## What is supported?
- All Entity Framework Core Version: EF Core 8, EF Core 7, EF Core 6, EF Core 5, EF Core 3, EF Core 2
- All Inheritances (TPC, TPH, TPT)
- Complex Type/Owned Entity Type
- Enum
- Value Converter (EF Core)
- And more!