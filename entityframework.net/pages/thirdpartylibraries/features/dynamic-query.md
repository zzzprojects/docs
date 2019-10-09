---
PermaID: 1000018
Name: Dynamic Query
---

# Dynamic Query

## Introduction

Dynamic Query allows you to perform dynamic where clause, select, order by, with string expression at runtime.

## Why Dynamic Query?

### Common Scenarios:

 - Use dynamic select clause with string expression
 - Use dynamic order by with string expression
 - Use dynamic where clause with string expression

## Google Related Searches

 - [Entity Framework Dynamic LINQ](https://www.google.com/search?q=entity+framework+dynamic+linq)
 - [Entity Framework Dynamic Where Clause](https://www.google.com/search?q=entity+framework+dynamic+where+clause)
 - [Entity Framework OrderBy String](https://www.google.com/search?q=entity+framework+orderby+string)

## StackOverflow Related Questions

 - [Building dynamic where clauses in LINQ to EF queries](https://stackoverflow.com/questions/14901430/building-dynamic-where-clauses-in-linq-to-ef-queries)
 - [Dynamic LINQ OrderBy on IEnumerable< T >](https://stackoverflow.com/questions/41244/dynamic-linq-orderby-on-ienumerablet?rq=1)
 - [How to Dynamically build Select as relates to Linq & Entity Framework](https://stackoverflow.com/questions/44441338/how-to-dynamically-build-select-as-relates-to-linq-entity-framework)
 - [Using dynamic linq in EF Linq to Entities](https://stackoverflow.com/questions/28721888/using-dynamic-linq-in-ef-linq-to-entities)
 - [c# - Dynamically generate linq select with nested properties](https://stackoverflow.com/questions/51753165/c-sharp-dynamically-generate-linq-select-with-nested-properties)

```csharp
var customersList1 = context.Customers
    .OrderByDynamic(c => "c.Name")
    .ToList();

var customersList2 = context.Customers
    .Include(x => x.Invoices)
    .Where(c => "c.Invoices.Count > 0")
    .OrderByDescendingDynamic(c => "c.Invoices.Count")
    .ToList();
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Classic](/ef-classic)	|FREE/PRO	|EF6|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Future|
|[Eval Expression.NET](/eval-expression-net)	|FREE/PRO	|All	|< 1 Day	|Yes	|Dynamic Query|
|[System.Linq.Dynamic](/system-linq-dynamic)	|FREE	|All	|< 1 Day	|Yes    |Dynamic Query  |