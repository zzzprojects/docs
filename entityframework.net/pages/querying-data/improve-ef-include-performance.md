---
PermaID: 1000070
Title: Entity Framework Improve Include Performance - Learn Why it is Slow
MetaDescription: Unlock the power of Entity Framework by improving your include performance. Learn about the cause that can make the Include method slow and how to improve the performance.
LastMod: 2025-06-17
Tags: query performance
---

# Entity Framework Improve Include Performance: Discover Why it is Slow

## How to Improve Entity Framework Include Performance?

Entity Framework does a great job generating SQL. However, it is often far from being optimized.

```csharp
using (var ctx = new CustomerContext())
{
    var customers = ctx.Customers
        .Include(x => x.Orders.Select(y => y.Items.Select(z => z.Product))
        .Include(x => x.Payments.Select(y => y.Items)
        .Include(x => x.Shippings.Select(y => y.Items)
        .ToList();
}
```

### StackOverflow Related Questions

 - [Entity Framework Include performance](https://stackoverflow.com/questions/40155092/entity-framework-include-performance)
 - [Entity-framework code is slow when using Include() many times](https://stackoverflow.com/questions/34724196/entity-framework-code-is-slow-when-using-include-many-times)
 - [Entity Framework .include Performance Issue](https://stackoverflow.com/questions/39095636/entity-framework-include-performance-issue)
 - [Entity Framework performance of include](https://stackoverflow.com/questions/32579571/entity-framework-performance-of-include)

## Answer

 - SPLIT the LINQ query in multiple queries
 - USE EF+ Query IncludeOptimized (**Recommended**)

### SPLIT the LINQ query into multiple queries

You don't have to include everything in the same query, the divide and conquer strategy can apply here also!

#### Pros

 - Convert the BIG monster query generated by Entity Framework into multiple tiny monsters
 - Return fewer NULL values

#### Cons

 - Can sometimes be slower than Include
 - Cannot be used with AsNoTracking()
 - Multiple database round-trips are required

#### How?

 1. SPLIT the queries in multiple smaller queries
 2. Done!

```csharp
using (var ctx = new CustomerContext())
{
    // 1. SPLIT the queries into multiple smaller queries
    var customers = ctx.Customers
            .Include(x => x.Orders.Select(y => y.Items.Select(z => z.Product))
            .ToList();
                    
    var customers = ctx.Customers
            .Include(x => x.Payments.Select(y => y.Items)
            .ToList();
                       
    var customers = ctx.Customers
            .Include(x => x.Shippings.Select(y => y.Items)
            .ToList();
                     
    // 2. Done!
}
```

### USE EF+ Query IncludeOptimized (**Recommended**)

[Entity Framework Plus](/ef-plus) library contains the IncludeOptimized extension method which under the hood also splits the query into multiple queries but way more:

 - Easier
 - Faster
 - Flexible

#### Pros

 - Easier to use than splitting queries
 - One database round-trip is required
 - Bonus: Allows you to filter related entities

#### Cons

 - Can sometimes be slower than Include
 - Cannot be used with AsNoTracking()
 - Cannot be mixed with Include

#### How?

 1. CHANGE all Include by IncludeOptimized
 2. Done!
 

```csharp
using (var ctx = new CustomerContext())
{
    // 1. CHANGE all Include by IncludeOptimized
    var customers = ctx.Customers
            .IncludeOptimized(x => x.Orders.Select(y => y.Items.Select(z => z.Product))
            .IncludeOptimized(x => x.Payments.Select(y => y.Items)
            .IncludeOptimized(x => x.Shippings.Select(y => y.Items)
            .ToList();
                       
    // 2. Done!
}
```

### SQL Generated for Include vs IncludeOptimized

People looking at the SQL generated often become speechless.


```csharp
SELECT
[UnionAll2].[ID] AS [C1],
[UnionAll2].[ID1] AS [C2],
[UnionAll2].[Code] AS [C3],
[UnionAll2].[CreatedDate] AS [C4],
[UnionAll2].[C1] AS [C5],
[UnionAll2].[ID2] AS [C6],
[UnionAll2].[ID3] AS [C7],
[UnionAll2].[Code1] AS [C8],
[UnionAll2].[Customer_ID] AS [C9],
[UnionAll2].[C2] AS [C10],
[UnionAll2].[ID4] AS [C11],
[UnionAll2].[ID5] AS [C12],
[UnionAll2].[Code2] AS [C13],
[UnionAll2].[ID6] AS [C14],
[UnionAll2].[Code3] AS [C15],
[UnionAll2].[Order_ID] AS [C16],
[UnionAll2].[C3] AS [C17],
[UnionAll2].[C4] AS [C18],
[UnionAll2].[C5] AS [C19],
[UnionAll2].[C6] AS [C20],
[UnionAll2].[C7] AS [C21],
[UnionAll2].[C8] AS [C22],
[UnionAll2].[C9] AS [C23],
[UnionAll2].[C10] AS [C24],
[UnionAll2].[C11] AS [C25],
[UnionAll2].[C12] AS [C26],
[UnionAll2].[C13] AS [C27],
[UnionAll2].[C14] AS [C28],
[UnionAll2].[C15] AS [C29],
[UnionAll2].[C16] AS [C30],
[UnionAll2].[C17] AS [C31],
[UnionAll2].[C18] AS [C32],
[UnionAll2].[C19] AS [C33],
[UnionAll2].[C20] AS [C34]
FROM (SELECT
CASE WHEN ([Join2].[ID1] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C1],
[Extent1].[ID] AS [ID],
[Extent1].[ID] AS [ID1],
[Extent1].[Code] AS [Code],
[Extent1].[CreatedDate] AS [CreatedDate],
[Join2].[ID1] AS [ID2],
[Join2].[ID1] AS [ID3],
[Join2].[Code1] AS [Code1],
[Join2].[Customer_ID] AS [Customer_ID],
CASE WHEN ([Join2].[ID1] IS NULL) THEN CAST(NULL AS int) WHEN ([Join2].[ID2] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2],
[Join2].[ID2] AS [ID4],
[Join2].[ID2] AS [ID5],
[Join2].[Code2] AS [Code2],
[Join2].[ID3] AS [ID6],
[Join2].[Code3] AS [Code3],
[Join2].[Order_ID] AS [Order_ID],
CAST(NULL AS int) AS [C3],
CAST(NULL AS int) AS [C4],
CAST(NULL AS varchar(1)) AS [C5],
CAST(NULL AS int) AS [C6],
CAST(NULL AS int) AS [C7],
CAST(NULL AS int) AS [C8],
CAST(NULL AS int) AS [C9],
CAST(NULL AS varchar(1)) AS [C10],
CAST(NULL AS int) AS [C11],
CAST(NULL AS int) AS [C12],
CAST(NULL AS int) AS [C13],
CAST(NULL AS varchar(1)) AS [C14],
CAST(NULL AS int) AS [C15],
CAST(NULL AS int) AS [C16],
CAST(NULL AS int) AS [C17],
CAST(NULL AS int) AS [C18],
CAST(NULL AS varchar(1)) AS [C19],
CAST(NULL AS int) AS [C20]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent1]
LEFT OUTER JOIN (SELECT [Extent2].[ID] AS [ID1], [Extent2].[Code] AS [Code1], [Extent2].[Customer_ID] AS [Customer_ID], [Join1].[ID2], [Join1].[Code2], [Join1].[Order_ID], [Join1].[ID3], [Join1].[Code3]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Order] AS [Extent2]
LEFT OUTER JOIN (SELECT [Extent3].[ID] AS [ID2], [Extent3].[Code] AS [Code2], [Extent3].[Order_ID] AS [Order_ID], [Extent4].[ID] AS [ID3], [Extent4].[Code] AS [Code3]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_OrderItem] AS [Extent3]
LEFT OUTER JOIN [dbo].[EF6_Performance_Include_IncludeOptimized_Product] AS [Extent4] ON [Extent3].[Product_ID] = [Extent4].[ID] ) AS [Join1] ON [Extent2].[ID] = [Join1].[Order_ID] ) AS [Join2] ON [Extent1].[ID] = [Join2].[Customer_ID]
UNION ALL
SELECT
2 AS [C1],
[Extent5].[ID] AS [ID],
[Extent5].[ID] AS [ID1],
[Extent5].[Code] AS [Code],
[Extent5].[CreatedDate] AS [CreatedDate],
CAST(NULL AS int) AS [C2],
CAST(NULL AS int) AS [C3],
CAST(NULL AS varchar(1)) AS [C4],
CAST(NULL AS int) AS [C5],
CAST(NULL AS int) AS [C6],
CAST(NULL AS int) AS [C7],
CAST(NULL AS int) AS [C8],
CAST(NULL AS varchar(1)) AS [C9],
CAST(NULL AS int) AS [C10],
CAST(NULL AS varchar(1)) AS [C11],
CAST(NULL AS int) AS [C12],
[Join4].[ID4] AS [ID2],
[Join4].[ID4] AS [ID3],
[Join4].[Code4] AS [Code1],
[Join4].[Customer_ID] AS [Customer_ID],
CASE WHEN ([Join4].[ID5] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C13],
[Join4].[ID5] AS [ID4],
[Join4].[ID5] AS [ID5],
[Join4].[Code5] AS [Code2],
[Join4].[Payment_ID] AS [Payment_ID],
CAST(NULL AS int) AS [C14],
CAST(NULL AS int) AS [C15],
CAST(NULL AS varchar(1)) AS [C16],
CAST(NULL AS int) AS [C17],
CAST(NULL AS int) AS [C18],
CAST(NULL AS int) AS [C19],
CAST(NULL AS int) AS [C20],
CAST(NULL AS varchar(1)) AS [C21],
CAST(NULL AS int) AS [C22]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent5]
INNER JOIN (SELECT [Extent6].[ID] AS [ID4], [Extent6].[Code] AS [Code4], [Extent6].[Customer_ID] AS [Customer_ID], [Extent7].[ID] AS [ID5], [Extent7].[Code] AS [Code5], [Extent7].[Payment_ID] AS [Payment_ID]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Payment] AS [Extent6]
LEFT OUTER JOIN [dbo].[EF6_Performance_Include_IncludeOptimized_PaymentItem] AS [Extent7] ON [Extent6].[ID] = [Extent7].[Payment_ID] ) AS [Join4] ON [Extent5].[ID] = [Join4].[Customer_ID]
UNION ALL
SELECT
3 AS [C1],
[Extent8].[ID] AS [ID],
[Extent8].[ID] AS [ID1],
[Extent8].[Code] AS [Code],
[Extent8].[CreatedDate] AS [CreatedDate],
CAST(NULL AS int) AS [C2],
CAST(NULL AS int) AS [C3],
CAST(NULL AS varchar(1)) AS [C4],
CAST(NULL AS int) AS [C5],
CAST(NULL AS int) AS [C6],
CAST(NULL AS int) AS [C7],
CAST(NULL AS int) AS [C8],
CAST(NULL AS varchar(1)) AS [C9],
CAST(NULL AS int) AS [C10],
CAST(NULL AS varchar(1)) AS [C11],
CAST(NULL AS int) AS [C12],
CAST(NULL AS int) AS [C13],
CAST(NULL AS int) AS [C14],
CAST(NULL AS varchar(1)) AS [C15],
CAST(NULL AS int) AS [C16],
CAST(NULL AS int) AS [C17],
CAST(NULL AS int) AS [C18],
CAST(NULL AS int) AS [C19],
CAST(NULL AS varchar(1)) AS [C20],
CAST(NULL AS int) AS [C21],
[Join6].[ID6] AS [ID2],
[Join6].[ID6] AS [ID3],
[Join6].[Code6] AS [Code1],
[Join6].[Customer_ID] AS [Customer_ID],
CASE WHEN ([Join6].[ID7] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C22],
[Join6].[ID7] AS [ID4],
[Join6].[ID7] AS [ID5],
[Join6].[Code7] AS [Code2],
[Join6].[Shipping_ID] AS [Shipping_ID]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent8]
INNER JOIN (SELECT [Extent9].[ID] AS [ID6], [Extent9].[Code] AS [Code6], [Extent9].[Customer_ID] AS [Customer_ID], [Extent10].[ID] AS [ID7], [Extent10].[Code] AS [Code7], [Extent10].[Shipping_ID] AS [Shipping_ID]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Shipping] AS [Extent9]
LEFT OUTER JOIN [dbo].[EF6_Performance_Include_IncludeOptimized_ShippingItem] AS [Extent10] ON [Extent9].[ID] = [Extent10].[Shipping_ID] ) AS [Join6] ON [Extent8].[ID] = [Join6].[Customer_ID]) AS [UnionAll2]
ORDER BY [UnionAll2].[ID1] ASC, [UnionAll2].[C1] ASC, [UnionAll2].[ID3] ASC, [UnionAll2].[C2] ASC, [UnionAll2].[C4] ASC, [UnionAll2].[C7] ASC, [UnionAll2].[C13] ASC, [UnionAll2].[C16] ASC
```

Did you really scroll down all the code to see all the SQL generated to execute this query?

***Suprised? Astonished? Flabbergasted?***

Yes, this is what I call a monster SQL, and if you execute it, most columns will contain a NULL value which makes it even worse.

Let's now look what's the SQL generated by IncludeOptimized method


```csharp
//EF+ Query Future: 1 of 4

SELECT
[Project2].[ID] AS [ID],
[Project2].[C2] AS [C1],
[Project2].[ID1] AS [ID1],
[Project2].[C1] AS [C2],
[Project2].[ID2] AS [ID2],
[Project2].[Code] AS [Code]
FROM ( SELECT
[Extent1].[ID] AS [ID],
[Join2].[ID1] AS [ID1],
[Join2].[ID2] AS [ID2],
[Join2].[Code1] AS [Code],
[Join2].[C1] AS [C1],
CASE WHEN ([Join2].[ID1] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent1]
LEFT OUTER JOIN (SELECT [Extent2].[ID] AS [ID1], [Extent2].[Customer_ID] AS [Customer_ID], [Project1].[ID] AS [ID2], [Project1].[Code] AS [Code1], [Project1].[C1] AS [C1]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Order] AS [Extent2]
LEFT OUTER JOIN (SELECT
[Extent3].[Order_ID] AS [Order_ID],
[Extent4].[ID] AS [ID],
[Extent4].[Code] AS [Code],
1 AS [C1]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_OrderItem] AS [Extent3]
LEFT OUTER JOIN [dbo].[EF6_Performance_Include_IncludeOptimized_Product] AS [Extent4] ON [Extent3].[Product_ID] = [Extent4].[ID] ) AS [Project1] ON [Extent2].[ID] = [Project1].[Order_ID] ) AS [Join2] ON [Extent1].[ID] = [Join2].[Customer_ID]
) AS [Project2]
ORDER BY [Project2].[ID] ASC, [Project2].[C2] ASC, [Project2].[ID1] ASC, [Project2].[C1] ASC
;

// EF+ Query Future: 2 of 4

SELECT
[Project1].[ID] AS [ID],
[Project1].[C2] AS [C1],
[Project1].[ID1] AS [ID1],
[Project1].[C1] AS [C2],
[Project1].[ID2] AS [ID2],
[Project1].[Code] AS [Code],
[Project1].[Payment_ID] AS [Payment_ID]
FROM ( SELECT
[Extent1].[ID] AS [ID],
[Join1].[ID1] AS [ID1],
[Join1].[ID2] AS [ID2],
[Join1].[Code1] AS [Code],
[Join1].[Payment_ID] AS [Payment_ID],
CASE WHEN ([Join1].[ID1] IS NULL) THEN CAST(NULL AS int) WHEN ([Join1].[ID2] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C1],
CASE WHEN ([Join1].[ID1] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent1]
LEFT OUTER JOIN (SELECT [Extent2].[ID] AS [ID1], [Extent2].[Customer_ID] AS [Customer_ID], [Extent3].[ID] AS [ID2], [Extent3].[Code] AS [Code1], [Extent3].[Payment_ID] AS [Payment_ID]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Payment] AS [Extent2]
LEFT OUTER JOIN [dbo].[EF6_Performance_Include_IncludeOptimized_PaymentItem] AS [Extent3] ON [Extent2].[ID] = [Extent3].[Payment_ID] ) AS [Join1] ON [Extent1].[ID] = [Join1].[Customer_ID]
) AS [Project1]
ORDER BY [Project1].[ID] ASC, [Project1].[C2] ASC, [Project1].[ID1] ASC, [Project1].[C1] ASC
;

//EF+ Query Future: 3 of 4

SELECT
[Project1].[ID] AS [ID],
[Project1].[C2] AS [C1],
[Project1].[ID1] AS [ID1],
[Project1].[C1] AS [C2],
[Project1].[ID2] AS [ID2],
[Project1].[Code] AS [Code],
[Project1].[Shipping_ID] AS [Shipping_ID]
FROM ( SELECT
[Extent1].[ID] AS [ID],
[Join1].[ID1] AS [ID1],
[Join1].[ID2] AS [ID2],
[Join1].[Code1] AS [Code],
[Join1].[Shipping_ID] AS [Shipping_ID],
CASE WHEN ([Join1].[ID1] IS NULL) THEN CAST(NULL AS int) WHEN ([Join1].[ID2] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C1],
CASE WHEN ([Join1].[ID1] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent1]
LEFT OUTER JOIN (SELECT [Extent2].[ID] AS [ID1], [Extent2].[Customer_ID] AS [Customer_ID], [Extent3].[ID] AS [ID2], [Extent3].[Code] AS [Code1], [Extent3].[Shipping_ID] AS [Shipping_ID]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Shipping] AS [Extent2]
LEFT OUTER JOIN [dbo].[EF6_Performance_Include_IncludeOptimized_ShippingItem] AS [Extent3] ON [Extent2].[ID] = [Extent3].[Shipping_ID] ) AS [Join1] ON [Extent1].[ID] = [Join1].[Customer_ID]
) AS [Project1]
ORDER BY [Project1].[ID] ASC, [Project1].[C2] ASC, [Project1].[ID1] ASC, [Project1].[C1] ASC
;

//EF+ Query Future: 4 of 4

SELECT
[Extent1].[ID] AS [ID],
[Extent1].[Code] AS [Code],
[Extent1].[CreatedDate] AS [CreatedDate]
FROM [dbo].[EF6_Performance_Include_IncludeOptimized_Customer] AS [Extent1]
ORDER BY [Extent1].[ID] ASC;
```

That's for sure not the most optimized SQL, but it's already way more readable.