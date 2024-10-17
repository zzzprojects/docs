---
title: Dapper Query, QueryAsync
description: The Dapper Query and QueryAsync methods are used to select data from your database and return a list of objects or strongly typed types.
canonical: /dapper-query/selecting-multiple-rows
status: Published
lastmod: 2024-10-17
---

# Querying Multiple Rows With Dapper

You can use Dapper to access a database and write queries in a more object-oriented manner. For example, it allows you to execute SQL commands and map the resulting data into a list of objects. With Dapper, you can easily select data from a database and project the result into a list of objects.

Dapper provides several methods for selecting data, depending on how you want to work with the data you retrieve. 


| Method | Description |
|:---|:---|
|`Query` |  Returns an enumerable of `dynamic` types | 
|`Query<T>` | Returns an enumerable of the type specified by the `T` parameter | 
|`QueryAsync` | Returns an enumerable of `dynamic` types asynchronously | 
|`QueryAsync<T>` | Returns an enumerable of the type specified by the `T` parameter asynchronously | 


To select data from a table or view using Dapper, you need to create an instance of the `IDbConnection` interface. Then you can use the `Query<T>()` or `QueryAsync<T>` method to execute the `SELECT` query and store the results in a list of objects.

## Dapper Query

The primary methods used for selecting data are `Query` and `Query<T>` and their `async` counterparts. The difference between `Query` and `Query<T>` is that `Query` returns results as an `IEnumerable<dynamic>` and the `Query<T>` returns an `IEnumerable` of the type specified by the type argument. The following examples query a `Product` table.

For example, suppose you have a database table called **Product**. You could use the following code to select all rows from the **Product** table and project them into a dynamic list of objects:

```csharp
var sql = "SELECT * FROM Product WHERE CategoryID = @categoryID";
var products = connection.Query(sql, new { categoryID = 1 }).ToList();

foreach(var product in products)
{
	Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
}	
```

[Online Example](https://dotnetfiddle.net/JFyNuR)

:::{.alert .alert-info}
Note that there is no explicit call to open the connection in the code above. Instead, Dapper will check the state of the connection and open it if it is closed. 
:::

Working with dynamic types should ordinarily be avoided because the risk of introducing run-time errors is high. It can be easy to mistype a property name, especially as they are case-sensitive. For example, if you referred to the key column as `ProductId` instead of `ProductID`, there would be no help from Intellisense or exceptions raised. Nothing would be output to the console window at all. Therefore the recommendation is to use the `Query<T>` methods that takes a type parameter representing the data type that the query returns. 

The next example works with a `Product` type, which, following the table schema, has the following definition:

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
var sql = "SELECT * FROM Product WHERE CategoryID = @categoryID";
var products = connection.Query<Product>(sql, new { categoryID = 1 }).ToList();

foreach(var product in products)
{
	Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
}
```

[Online Example](https://dotnetfiddle.net/cEAe4g)

## Dapper QueryAsync

To execute the query asynchronously, Dapper provides the `QueryAsync` method, an asynchronous version of the `Query` method.

```csharp
var sql = "SELECT * FROM Product WHERE CategoryID = @categoryID";
var products = (await connection.QueryAsync(sql, new { categoryID = 1 })).ToList();

foreach(var product in products)
{
	Console.WriteLine($"ProductID: {product.ProductID}; Name: {product.Name}");
}	
```

[Online Example](https://dotnetfiddle.net/PDrvxG)

The asynchronous version of the `Query<T>`  method is `QueryAsync<T>`:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT * FROM Customers";
    var customers = await connection.QueryAsync<Customer>(sql);
	
    foreach(var customer in customers)
    {
        Console.WriteLine($"{customer.CustomerID} {customer.CompanyName}");
    }
}
```

[Online Example](https://dotnetfiddle.net/FqAf9K)

:::{.alert .alert-info}
Note if you want to use `QueryAsync` with `buffered = false`, you should use instead the [QueryUnbufferedAsync](/dapper-query/selecting-unbuffered-async) method.
:::

## Related Articles

- [Querying](/dapper-query)
- [Query Unbuffered Async](/dapper-query/selecting-unbuffered-async)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)
- [Buffered & Unbuffered Queries](/misc/buffered-unbuffered)