---
Permalink: ef-core-query-hint
Name: Query Hint
LastMod: 2024-10-18
---

# Query Hint

## Description

The Query Hint feature allows you to add hints to your queries with an easy-to-use syntax.

For example, consider the following LINQ query: `context.Products.ToList()`. This query generates the following SQL:

```sql
SELECT [p].[ProductID], [p].[Description], [p].[Name]
FROM [Products] AS [p]
```

If you want to add a hint such as `NOLOCK` to this query, there is currently no easy way through EF Core.

However, the Query Hint feature allows you to do it easily by changing the LINQ to:

```csharp
context.Products.WithHint(SqlServerTableHintFlags.NOLOCK).ToList();
```

This will generate the following SQL:

```sql
SELECT [p].[ProductID], [p].[Description], [p].[Name]
FROM [Products] AS [p] WITH (NOLOCK)
```

## Supported SQL Server TableHint

You can specify hints directly as a string, for example:

```csharp
context.Products.WithHint("NOLOCK").ToList();
```

You can also specify more than one hint:

```csharp
context.Products.WithHint("NOLOCK, INDEX(IX_ProductName)").ToList();
```

Additionally, you can use one of the following `SqlServerTableHintFlags`:

```csharp
// context.Products.WithHint(SqlServerTableHintFlags.NOLOCK).ToList();

namespace Z.EntityFramework.Plus
{
    [Flags]
    public enum SqlServerTableHintFlags
    {
        NONE = 0,
        FORCESCAN = 1,
        FORCESEEK = 2,
        HOLDLOCK = 4,
        NOLOCK = 8,
        NOWAIT = 16,
        PAGLOCK = 32,
        READCOMMITTED = 64,
        READCOMMITTEDLOCK = 256,
        READPAST = 512,
        READUNCOMMITTED = 1024,
        REPEATABLEREAD = 2048,
        ROWLOCK = 4096,
        SERIALIZABLE = 8192,
        SNAPSHOT = 16384,
        TABLOCK = 262144,
        TABLOCKX = 16777216,
        UPDLOCK = 33554432,
        XLOCK = 67108864
    }
}
```

## How to Specify "Hints" for Particular Entity Types

When using the `.Include` method, or in scenarios where you need to apply the same SQL hints across multiple entity types, you can pass a list of types as the second parameter. This approach allows you to fine-tune query performance by specifying hints, such as `NOLOCK`, for targeted entities within your query.

Below are examples demonstrating how to apply hints selectively:

```csharp
// Apply the NOLOCK hint only to OrderItem
context.Orders
    .Include(x => x.Items)
    .WithHint(SqlServerTableHintFlags.NOLOCK, typeof(OrderItem))
    .ToList();

// Apply the NOLOCK hint to both Order and OrderItem
context.Orders
    .Include(x => x.Items)
    .WithHint(SqlServerTableHintFlags.NOLOCK, typeof(Order), typeof(OrderItem))
    .ToList();
```

This method ensures that your hints are applied precisely where needed, enhancing query flexibility and performance.