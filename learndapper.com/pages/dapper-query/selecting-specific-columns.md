---
title: Dapper Query Specific Columns
description: All Dapper querying methods allow you to select specific columns from your tables. Only matching properties will be mapped with the result.
canonical: /dapper-query/selecting-specific-columns
status: Published
lastmod: 2024-10-17
---

# Querying Specific Columns With Dapper

Dapper allows you to select a subset of columns in your query results easily. That makes it easier to optimize queries for performance and readability, as well as gives you more control over how data is returned from your queries. 

 - To do this, simply provide a comma-separated list of column names within the SQL statement for your query. 

For example:

```csharp
SELECT col1, col2,  col3 FROM MyTable 
```

The above query will return a result set containing only the specified columns. That is useful when working with large tables and optimizing your queries by retrieving only the data you need. It also makes it easier to read and understand the structure of the result set. 

Most examples in other tutorials used the wildcard character (`*`) to include all columns in the target table. However, more often than not, you only want to retrieve data from a subset of columns. You do this by restricting the list of columns in the SQL only to include the ones you want, but you can still pass the relevant object into the type parameter of the `Query<T>` method. Properties of the object that aren't featured in the column list will be set to their default values. 

In the following example, the `CategoryID` column in the `Product` table is not included as part of the select statement, but it is used in a filter:

```csharp
var sql = "SELECT ProductID, Name FROM Product WHERE CategoryID = @categoryID";
var products = connection.Query(sql, new { categoryID = 1 }).ToList();

foreach(var product in products)
{
	Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}; Description: {product.CategoryID}");
}	
```

[Online Example](https://dotnetfiddle.net/5Znqq2)

Although only those rows were selected where `CategoryID` is `1`, the output shows that the CategoryID property on the generated objects is `0` (the default value of a type `int`) in all cases. That is because no value was made available for Dapper to map to the objects it created, so the property values are set to their defaults.

If you try to reference a column that isn't included in the select list when returning dynamic types, the value will always be null:

```csharp
var sql = "SELECT ProductID, Name FROM Product WHERE CategoryID = @categoryID";
var products = connection.Query<Product>(sql, new { categoryID = 1 }).ToList();

foreach(var product in products)
{
	Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}; Description: {product.CategoryID}");
}	
```

[Online Example](https://dotnetfiddle.net/HfqoHc)

No value is printed for the `Description` property.

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)