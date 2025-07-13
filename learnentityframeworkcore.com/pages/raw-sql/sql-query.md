---
title: Executing Raw SQL Queries using SqlQuery Method
description: The SqlQuery method in Entity Framework Core is used to execute raw SQL queries against the database and return the results as scalar values as result. 
canonical: /raw-sql/sql-query
status: Published
lastmod: 2025-07-13
---

# EF Core SqlQuery

In Entity Framework Core (EF Core), querying scalar types involves using LINQ to query the data in the database and retrieve the scalar values as a result. 

 - Scalar values are single values such as integers, strings, or dates, as opposed to complex types. 
 - To query scalar types, you can use the `Select` operator in LINQ to select the specific scalar values that you want to retrieve, and then call `ToList()` or `FirstOrDefault()` on the query to execute it and retrieve the results. 

For example:

```csharp
using (var context = new LibraryContext())
{
    var age = context.Authors
        .Where(x => x.Name == "William")
        .Select(x => x.Age)
        .FirstOrDefault();
}
```

## DbSet.SqlQuery

EF Core also provides the `SqlQuery` method that can execute raw SQL queries that return scalar or non-entity types. While `FromSql` is useful for querying entities defined in your model, `SqlQuery` allows you to easily query for scalar, non-entity types via SQL, without needing to drop down to lower-level data access APIs.

Here is an example of how to use the `SqlQuery` method to return a scalar value.

```csharp
using (var context = new LibraryContext())
{
    var results = context.Database.SqlQuery<int>($"SELECT COUNT(*) FROM Books");
}
```

In this example, the query returns a single integer value, which is the count of rows in the `Books` table. 

You can also compose LINQ operators over your SQL query. However, since your SQL becomes a subquery whose output column needs to be referenced by the SQL EF adds, you must name the output column Value. 

For example, the following query returns the IDs which are above the ID average:

```csharp
var overAverageIds = context.Database
    .SqlQuery<int>($"SELECT [BlogId] AS [Value] FROM [Blogs]")
    .Where(id => id > context.Blogs.Average(b => b.BlogId))
    .ToList();
```

The `SqlQueryRaw` allows for the dynamic construction of SQL queries, just like FromSqlRaw does for entity types.

**Note** that when using the `SqlQuery` method to return scalar or non-entity types, it's important to ensure that the query returns a result set that corresponds to the specified generic type. If the result set does not match the type, an exception will be thrown.
