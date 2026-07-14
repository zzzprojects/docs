---
Permalink: ef-core-query-hint
Name: Query Hint
LastMod: 2026-07-14
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

## Supported SQL Server TableHints

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

## Limitations

* This limitation only affects EF Core 10. Some scenarios using [ExecuteUpdate](/execute-update) do not work correctly when combined with `TagWith`, especially queries that use the equality operator (`==`). Other query patterns may also be affected. The issue was introduced in EF Core 10 and fixed by Microsoft in EF Core 11. [See more](https://github.com/dotnet/efcore/issues/36908).

For example, the following query correctly includes the tag:

```csharp
await context.Customers
    .Where(x => x.CustomerID > 1)
    .TagWith("TagWith will work")
    .ExecuteUpdateAsync(x => x.SetProperty(y => y.Name, "Test"));
```

However, the following query does not include the tag:

```csharp
await context.Customers
    .Where(x => x.CustomerID == 1)
    .TagWith("TagWith will not work")
    .ExecuteUpdateAsync(x => x.SetProperty(y => y.Name, "Test"));
```

This issue is resolved in EF Core 11.

[Online Example](https://dotnetfiddle.net/TJZ6Fo)