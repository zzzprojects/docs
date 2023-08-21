---
title: Dapper Query Specific Columns
description: All Dapper querying methods allow you to select specific columns from your tables. Only matching properties will be mapped with the result.
canonical: /dapper-query/selecting-specific-columns
status: Published
lastmod: 2023-08-01
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

In the following example, the _Discontinued_ column in the _Products_ table is not included as part of the select statement, but it is used in a filter:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT ProductID, ProductName FROM Products WHERE Discontinued = 1";
    var products = connection.Query<Product>(sql);
	
    foreach (var product in products)
    {
        Console.WriteLine($"{product.ProductID} {product.ProductName}: {product.Discontinued}");
    }
}
```

Although only those rows were selected where `Discontinued` is `true`, the output shows that the Discontinued property on the generated objects is `false` in all cases. That is because no value was made available for Dapper to map to the objects it created, so the property values are set to their defaults.

![Image](/images/28-03-2019-08-10-02.png)

If you try to reference a column that isn't included in the select list when returning dynamic types, the value will always be null:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT ProductID, ProductName FROM Products WHERE discontinued = 1";
    var products = connection.Query(sql);
	
    foreach (var product in products)
    {
        Console.WriteLine($"{product.ProductID} {product.ProductName}: {product.Discontinued}");
    }
}
```
No value is printed to the output for the `Discontinued` property:

![Image](/images/28-03-2019-08-37-00.png)

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)

