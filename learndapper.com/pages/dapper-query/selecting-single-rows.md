---
title: Dapper QuerySingle, QuerySingleOrDefault, QueryFirst, QueryFirstOrDefault
description: The Dapper QuerySingle and QueryFirst methods allow you to return a single row of data and map it to a dynamic object or the generic entity type.
canonical: /dapper-query/selecting-single-rows
status: Published
lastmod: 2025-06-15
---

# Querying a Single Row With Dapper

Dapper provides several methods for selecting a single row of data, depending on how you want to work with the data you retrieve. 

| Method | Description | Exception |
|:---|:---|:---|
|`QuerySingle` | Use when only one row is expected to be returned. Returns a `dynamic` type | `InvalidOperationException`, when the query returns zero or more than one element |
|`QuerySingle<T>` | Use when only one row is expected to be returned. Returns an instance of the type specified by the `T` type parameter | `InvalidOperationException`, when the query returns zero or more than one element |
|`QuerySingleOrDefault` | Use when zero or one row is expected to be returned. Returns a `dynamic` type or `null` | `InvalidOperationException`, when the query returns more than one element |
|`QuerySingleOrDefault<T>` | Use when zero or one row is expected to be returned. Returns an instance of the type specified by the `T` type parameter or `null` | `InvalidOperationException`, when the query returns more than one element |
|`QueryFirst` | Returns the first row as a `dynamic` type | `InvalidOperationException`, when the query returns zero elements |
|`QueryFirst<T>` | Returns the first row as an instance of the type specified by the `T` type parameter | `InvalidOperationException`, when the query returns zero elements |
|`QueryFirstOrDefault` | Returns the first row as a `dynamic` type or `null` if no results are returned |   |
|`QueryFirstOrDefault<T>` | Returns the first row as an instance of the type specified by the `T` type parameter or `null` if no results are returned|    |

## Dapper QuerySingle

Dapper allows developers to easily select a single row of data from the database by using its `QuerySingle` method. This method takes the SQL query and any associated parameters as arguments and returns an instance of the specified type.

 - The `QuerySingle` and `QuerySingle<T>` methods are designed to be used when you expect only one row to match the criteria specified in the query. 
 - The difference between `QuerySingle` and `QuerySingle<T>` is that `QuerySingle` returns results as a  `dynamic` type whereas `QuerySingle<T>` returns an instance of the type represented by `T` type argument. 

The following examples query a `Product` table.

The first example returns dynamic types:

```csharp
var sql = "SELECT * FROM Product WHERE ProductID = @productID";
var product = connection.QuerySingle(sql, new { productID = 1 });

Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
```

[Online Example](https://dotnetfiddle.net/GIUai9)

The next example works with a `Product` type, which  has the following definition based on the table schema:

```csharp
public class Product
{
	public int ProductID { get; set; }
	public int CategoryID { get; set; }
	public string Name { get; set; }
	public string Description { get; set; }		
}
```

The following example is identical to the first one except for the presence of the `<Product>` type parameter:

```csharp
var sql = "SELECT * FROM Product WHERE ProductID = @productID";
var product = connection.QuerySingle<Product>(sql, new { productID = 1 });

Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
```

[Online Example](https://dotnetfiddle.net/Wq1NAZ)

No matter what type of data you select from your database, Dapper's QuerySingle method can help make the process much simpler. With its simple syntax and parameterized queries, you'll be able to easily select single rows of data from your database. 

## Dapper QuerySingleAsync

The `QuerySingleAsync` is an asynchronous version of `QuerySingle`, that executes the query asynchronously and returns the single row in the result set returned by the query.

```csharp
var sql = "SELECT * FROM Product WHERE ProductID = @productID";
var product = await connection.QuerySingleAsync(sql, new { productID = 1 });

Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
```

[Online Example](https://dotnetfiddle.net/LDe6ah)

The asynchronous version of the `QuerySingle<T>`  method is `QuerySingleAsync<T>`:

```csharp
var sql = "SELECT * FROM Product WHERE ProductID = @productID";
var product = await connection.QuerySingleAsync<Product>(sql, new { productID = 1 });

Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
```

[Online Example](https://dotnetfiddle.net/Cyocky)

## Dapper QueryFirst

Dapper `QueryFirst` allows you to collect the first result from a query. That is especially useful when you need only one row of data, such as when querying for an ID value or other single-row returns. QueryFirst can accept any type of SQL query and will return the result accordingly. 

```csharp
var sql = "SELECT * FROM Product WHERE ProductID = @productID";
var product = connection.QueryFirst(sql, new { productID = 1 });
// var product = connection.QueryFirst<Product>(sql, new { productID = 1 });

Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
```

[Online Example](https://dotnetfiddle.net/lfknXe)

## Dapper QueryFirstAsync

The asynchronous version of the `QueryFirst`  method is `QueryFirstAsync`:

```csharp
var sql = "SELECT * FROM Product WHERE ProductID = @productID";
var product = await connection.QueryFirstAsync(sql, new { productID = 1 });
// var product = await connection.QueryFirstAsync<Product>(sql, new { productID = 1 });

Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
```

[Online Example](https://dotnetfiddle.net/PqnUr6)

## Conclusion

In the above examples, we have used Dapper `QuerySingle` and `QueryFirst` methods to query a database and return only one row from the result set. That is useful when you know only one record will be returned, and you don't need to loop through all records in the result set. We can also access the values of the object returned from `QueryFirst` by specifying the name of each column. That provides a powerful and fast way to query a database with Dapper.

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)