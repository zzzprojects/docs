---
title: Dapper QueryMultiple, QueryMultipleAsync
description: The Dapper QueryMultiple method allows you to select multiple results from a database in a single query and then, after reading those results, map them.
canonical: /dapper-query/selecting-multiple-results
status: Published
lastmod: 2025-06-15
---

# Querying Multiple Results With Dapper

You can execute multiple queries in a single SQL statement using the `QueryMultiple` method (if the [database provider](/database-providers) supports it). Once executed, you can map the returned results to multiple objects using methods such as `Read<T>`, `ReadFirst<T>`, `ReadSingle<T>` and more.

The Dapper `QueryMultiple` method allows you to select multiple results from a database query. That feature is very useful for selecting multiple result sets at once, thus avoiding unnecessary round trips to the database server. 

 - The `QueryMultiple` method provides a simple way of fetching data stored in different tables or views with a single database query. 
 - By using dapper's `QueryMultiple` method, you can select multiple rows from different tables in one go, saving time and resources. 
 - Dapper's `QueryMultiple` method allows you to map results from multiple queries into strongly typed collections that can be easily consumed within your application code. 
 - Using Dapper `QueryMultiple` is an efficient way to select multiple results from a single database query. 
 - It saves time and resources by avoiding unnecessary round trips to the database server and eliminates the need for complex SQL queries. 

| Method | Description |
|:---|:---|:---|
|`QueryMultiple` |  Execute multiple queries within a single database command and return a `GridReader` to map the results to multiple objects | 
|`QueryMultipleAsync` | Execute multiple queries within a single database command and return a `GridReader` to map the results to multiple objects asynchronously | 

After using the `QueryMultiple` or `QueryMultipleAsync` method, you can use the following methods with the results:

| Method | Description |
|:---|:---|:---|
|`Read`, `ReadAsync` | Returns an enumerable of `dynamic` types | 
|`Read<T>`, `ReadAsync<T>` | Returns an enumerable of the type specified by the `T` parameter | 
|`ReadFirst`, `ReadFirstAsync` | Returns the first row as a `dynamic` type | 
|`ReadFirst<T>`, `ReadFirstAsync<T>` | Returns the first row as an instance of the type specified by the `T` type parameter | 
|`ReadFirstOrDefault`, `ReadFirstOrDefaultAsync` | Returns the first row as a `dynamic` type or `null` if no results are returned | 
|`ReadFirstOrDefault<T>`, `ReadFirstOrDefaultAsync<T>` | Returns the first row as an instance of the type specified by the `T` type parameter or `null` if no results are returned | 
|`ReadSingle`, `ReadSingleAsync` | Use when only one row is expected to be returned. Returns a `dynamic` type | 
|`ReadSingle<T>`, `ReadSingleAsync<T>` | Use when only one row is expected to be returned. Returns an instance of the type specified by the `T` type parameter | 
|`ReadSingleOrDefault`, `ReadSingleOrDefaultAsync` | Use when zero or one row is expected to be returned. Returns a `dynamic` type or `null` | 
|`ReadSingleOrDefault<T>`, `ReadSingleOrDefaultAsync<T>` | Use when zero or one row is expected to be returned. Returns an instance of the type specified by the `T` type parameter or `null` | 

## Dapper QueryMultiple

The following example demonstrates how to use Dapper `QueryMultiple` to execute multiple SQL statements in one query. 

```csharp
string sql = @"
SELECT * FROM [Order] WHERE OrderID = @orderID;
SELECT * FROM [OrderItem] WHERE OrderID = @orderID;
";

using (var multi = connection.QueryMultiple(sql, new {orderID = 1}))
{
   var order = multi.ReadFirst<Order>();
   var orderItems = multi.Read<OrderItem>().ToList();
 
   FiddleHelper.WriteTable("Order", new List<Order>() { order});
   FiddleHelper.WriteTable("OrderItem", orderItems);
}
```

[Online Example](https://dotnetfiddle.net/bHeSOS)

With this, we can execute multiple SQL statements in one query and read each result set from the `GridReader` object returned by the `QueryMultiple` method. That can help increase the performance of your application in cases where multiple queries need to be executed and their results combined. 

Using Dapper `QueryMultiple` is a great way to simplify data access and ensure that your application runs quickly and efficiently. With its easy-to-use API, you can quickly set up complex queries and access the data you need. 

## Dapper QueryMultipleAsync

The asynchronous version of the `QueryMultiple<T>`  method is `QueryMultipleAsync<T>`:

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

string sql = @"
SELECT * FROM [Order] WHERE OrderID = @orderID;
SELECT * FROM [OrderItem] WHERE OrderID = @orderID;
";

using (var multi = await connection.QueryMultipleAsync(sql, new {orderID = 1}))
{
   var order = multi.ReadFirst<Order>();
   var orderItems = multi.Read<OrderItem>().ToList();
 
   FiddleHelper.WriteTable("Order", new List<Order>() { order});
   FiddleHelper.WriteTable("OrderItem", orderItems);
}
```

[Online Example](https://dotnetfiddle.net/Zak9uk)

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)
- [Buffered & Unbuffered Queries](/misc/buffered-unbuffered)