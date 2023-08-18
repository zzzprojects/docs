---
title: Dapper Query, QueryAsync
description: The Dapper Query and QueryAsync methods are used to select data from your database and return a list of objects or strongly typed types.
canonical: /dapper-query/selecting-multiple-rows
status: Published
lastmod: 2023-08-01
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

The primary methods used for selecting data are `Query` and `Query<T>` and their `async` counterparts. The difference between `Query` and `Query<T>` is that `Query` returns results as an `IEnumerable<dynamic>` and the `Query<T>` returns an `IEnumerable` of the type specified by the type argument. The following examples query the _Customers_ table from the ubiquitous Microsoft Northwind sample database. The schema of the table is as follows:

![Northwind Customers Schema](/images/25-03-2019-09-47-06.png)

For example, suppose you have a database table called **Customers**. You could use the following code to select all rows from the **Customers** table and project them into a dynamic list of objects:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT * FROM Customers";
    var customers = connection.Query(sql);
	
    foreach(var customer in customers)
    {
        Console.WriteLine($"{customer.CustomerID} {customer.CompanyName}");
    }
}
```

:::{.alert .alert-info}
Note that there is no explicit call to open the connection in the code above. Instead, Dapper will check the state of the connection and open it if it is closed. 
:::

Working with dynamic types should ordinarily be avoided because the risk of introducing run-time errors is high. It can be easy to mistype a property name, especially as they are case-sensitive. For example, if you referred to the key column as `CustomerId` instead of `CustomerID`, there would be no help from Intellisense or exceptions raised. Nothing would be output to the console window at all. Therefore the recommendation is to use the `Query<T>` methods that takes a type parameter representing the data type that the query returns. 

The next example works with a `Customer` type, which, following the table schema, has the following definition:

```csharp
public class Customer
{
    public string CustomerID { get; set; }
    public string CompanyName { get; set; }
    public string ContactName { get; set; }
    public string ContactTitle { get; set; }
    public string Address { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string PostalCode { get; set; }
    public string Country { get; set; }
    public string Phone { get; set; }
    public string Fax { get; set; }
}
```

The following example is identical to the first one except for the presence of the `<Customer>` type parameter:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT * FROM Customers";
    var customers = connection.Query<Customer>(sql);
	
    foreach(var customer in customers)
    {
        Console.WriteLine($"{customer.CustomerID} {customer.CompanyName}");
    }
}
```

## Dapper QueryAsync

To execute the query asynchronously, Dapper provides the `QueryAsync` method, an asynchronous version of the `Query` method.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT * FROM Customers";
    var customers = await connection.QueryAsync(sql);
	
    foreach(var customer in customers)
    {
        Console.WriteLine($"{customer.CustomerID} {customer.CompanyName}");
    }
}
```

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

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-retrieve-multiple-rows-returned-by-a-stored-procedure-in-dapper" itemprop="name">How to retrieve multiple rows returned by a stored procedure in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To retrieve multiple rows returned by a [stored procedure](/stored-procedures), you need to specify the stored procedure name in the command text and specify the command type to `CommandType.StoredProcedure`.

1. First, create your stored procedure:

```sql
CREATE PROCEDURE MyStoredProcedure
AS
BEGIN
    SELECT * FROM Customer
END
```

2. Map returned data to an anonymous type or a strongly typed object:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();
}
```

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string Name { get; set; }
	
    // Add other properties as needed, to match all columns returned by the stored procedure
}
	
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query<Customer>("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-does-the-buffered-parameter-for-the-query-and-queryasync-methods-in-dapper" itemprop="name">What does the buffered parameter for the Query and QueryAsync methods in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

The [buffered parameter](/misc/buffered-unbuffered) in Dapper for the `Query` and `QueryAsync` methods controls whether the results of a query are loaded into memory all at once (buffered) or streamed one by one (unbuffered). If set to `true` (the default), all the data from the query is loaded into memory immediately. If set to `false`, each row is loaded and processed one at a time, saving memory but potentially increasing execution time.

```csharp
// Query result will be streamed one by one when `buffered = false`
var customers = connection.Query<Customer>(sql, buffered: false);
```

</div></div>
</details>

</div>

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)
- [Buffered & Unbuffered Queries](/misc/buffered-unbuffered)