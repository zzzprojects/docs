---
title: Dapper Querying - Learn How Use Query Method With Dapper
description: Dapper allows querying one or multiple rows and automatically map au the retrieved result to your model type. Learn more about available querying options.
canonical: /dapper-query
status: Published
lastmod: 2024-10-17
---

# Querying Data With Dapper

Querying a database through Dapper allows you to quickly and easily access your data with simple queries. 

 - It is designed to be fast, efficient, and intuitive so that users can quickly get the information they need in an organized manner. 
 - Users can save time by quickly creating queries that are customized to their specific needs. 
 - The user-friendly features make it easy to get the most out of your database, allowing you to focus on other aspects of your project. 

Querying data using Dapper is quite simple. You just need to provide the dapper select query and parameters, and after that, Dapper will automatically map the resulting columns to their corresponding properties in your model type. 

```csharp
var sql = "SELECT * FROM Product WHERE CategoryID = @categoryID";
var products = connection.Query<Product>(sql, new { categoryID = 1}).ToList();
```

[Online Example](https://dotnetfiddle.net/X7EBtc)

In this example, we create a SQL query string and then use the Query method to execute it. Dapper supports multiple types of result sets, such as;

- [Querying Scalar Values (ExecuteScalar)](/dapper-query/selecting-scalar-values)
- [Querying Single Rows (QuerySingle, QueryFirst)](/dapper-query/selecting-single-rows)
- [Querying Multiple Rows (Query)](/dapper-query/selecting-multiple-rows)
- [Querying Multiple Result (QueryMultiple)](/dapper-query/selecting-multiple-results)
- [Querying Specific Columns](/dapper-query/selecting-specific-columns)

It also includes support for [stored procedures](/stored-procedures) and [bulk operations](/bulk-operations). Also, Dapper makes it possible to query data efficiently with little code, making the development process faster and easier.

## Dapper Querying Async

Dapper also allows you to query your database asynchronously with `Async` method to help application developers improve the performance of their applications. Methods such as:

- [ExecuteScalarAsync](/dapper-query/selecting-scalar-values#dapper-executescalarasync)
- [QueryAsync](/dapper-query/selecting-multiple-rows#dapper-queryasync)
- [QueryUnbufferedAsync](/dapper-query/selecting-unbuffered-async)
- [QuerySingleAsync](/dapper-query/selecting-single-rows#dapper-querysingleasync)
- [QueryFirstAsync](/dapper-query/selecting-single-rows#dapper-queryfirstasync)
- [QueryMultipleAsync](/dapper-query/selecting-multiple-results#dapper-querymultipleasync)

Are a powerful way to execute database commands asynchronously.


For example, let's consider a scenario where we must get all records from the **Employee** table in our database. The following code snippet shows how to do this using the `QueryAsync` method.

```csharp
var sql = "SELECT * FROM Product WHERE CategoryID = @categoryID";
var products = await connection.QueryAsync<Product>(sql, new { categoryID = 1});
```

[Online Example](https://dotnetfiddle.net/OzwY7O)