---
Permalink: advanced-entityframework
Name: Entity Framework
---

# Entity Framework (Core)

## Client vs. Server Evaluation

### Differences in Entity Framework Core 2.x and 3.x

Prior to version 3.0, Entity Framework Core supported client evaluation anywhere in the query. For more information, see the [previous versions](https://docs.microsoft.com/en-us/ef/core/querying/client-eval#previous-versions).

So when using Dynamic LINQ with Entity Framework Core, the same logic applies. See this [help-page](https://docs.microsoft.com/en-us/ef/core/querying/client-eval) from Microsoft for more details.

When using the Entity Framework specific [NuGet](installation-nuget#install-entityframework-versions-from-the-dynamic-linq-library), some more functionality can be used.

## Like-operator

When using strongly typed LINQ, you can use the `Microsoft.EntityFrameworkCore.EF.Functions` which contains the [Like](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbfunctionsextensions.like?view=efcore-3.1) method.

And you can also use this in Dynamic LINQ, if the [ResolveTypesBySimpleName](advanced-configuration#resolvetypesbysimplename) is set to true.

```csharp
var example1 = Cars.Where(c => EF.Functions.Like(c.Brand, "%t%"));
example1.Dump();

var config = new ParsingConfig { ResolveTypesBySimpleName = true };
var example2 = Cars.Where(config, "DynamicFunctions.Like(Brand, \"%t%\")");
example2.Dump();
```

The generated SQL will be:

```sql
SELECT [c].[Key], [c].[Brand], [c].[Color], [c].[Vin], [c].[Year]
FROM [Cars] AS [c]
WHERE [c].[Brand] LIKE N'%t%'
GO
```

## Async Query Operators

When using **EntityFramework**, **Microsoft.EntityFrameworkCore** or **Z.EntityFramework.Classic** it's possible to use some additional **Async** query operators.
See [this page](basic-query-operators#async-query-operators) for the list.
