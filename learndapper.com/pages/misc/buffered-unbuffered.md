---
title: Dapper Buffered and Unbuffered Queries
description: Dive into Dapper's buffered and unbuffered queries in .NET. Discover their usage, implications on performance, pros, cons, and ideal scenarios for use.
canonical: /misc/buffered-unbuffered
status: Published
lastmod: 2023-08-01
---

# Buffered vs Unbuffered Queries With Dapper

When it comes to querying data using Dapper, there are two main types of queries:

 - Buffered Query
 - Unbuffered Query
 
A buffered query retrieves all rows from the data source at once into memory, while an unbuffered query streams the results one by one as the next result is requested.

All queries are buffered by default unless you specify the parameter `buffered: false`.

## Dapper Buffered Queries

Buffered queries store all the results in the memory before returning them to the caller. 

- The main advantage of buffered queries is that they are usually faster to execute since all results can be received in one go. 
- This makes them ideal for situations where you need to retrieve large amounts of data quickly. 
- The downside, however, is that they tend to consume more memory than unbuffered queries, as they need to store all the results in memory before returning them.

For example, if you execute a buffered query with the following statement:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT * FROM Customers";
	
    var customers = connection.Query<Customer>(sql);
	
    foreach(var customer in customers)
    {
        Console.WriteLine($"{customer.CustomerId} {customer.CompanyName}");
    }
}
```

The entire result set will be retrieved from the data source into memory and stored in the `customers` variable. That means that all rows from the query will be returned at once. 

## Dapper Unbuffered Queries

Unbuffered queries are often used when only a few rows of data need to be returned or when you want to keep memory usage down. 

- Since they return an enumerable object, it can be iterated over one at a time, and each row can be processed as soon as it's received. 
- However, this can be slower than buffered queries since each row must be read separately and processed before reaching the next one.
- It can [block other queries](https://github.com/DapperLib/Dapper/issues/1328#issuecomment-532549546) if the reader is open for too long, therefore you should consume data from your unbuffered queries as fast as possible. 

When using an unbuffered query, you must explicitly specify the `buffered: false` parameter for Dapper to stream the results instead of loading them into memory. Otherwise, Dapper will assume you want a buffered query and load all the results into memory. 

Let's execute an unbuffered query by specifying the `buffered: false` parameter for the `Query` method.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT * FROM Customers";
	
    var customers = connection.Query<Customer>(sql, buffered: false);
	
    foreach(var customer in customers)
    {
        Console.WriteLine($"{customer.CustomerId} {customer.CompanyName}");
    }
}
```

The results will be streamed one by one, as they are requested. That can provide better performance than a buffered query if you don't need to access all of the rows at once.

The following methods support unbuffered queries:

- [Query](/dapper-query/selecting-multiple-rows#dapper-query)
- [QueryAsync](/dapper-query/selecting-multiple-rows#dapper-queryasync)
- [Read](/dapper-query/selecting-multiple-results)
- [ReadAync](/dapper-query/selecting-multiple-results)

## Conclusion

Generally, when using Dapper, it's best to use buffered queries for large datasets and unbuffered queries for smaller data sets. However, depending on your application, it may also be a good idea to try out both options and see which one works best for your situation. Ultimately, the right choice depends on the particular needs of your application and how much performance optimization is required.

## Related Articles

- [Querying](/dapper-query)
- [Query / QueryAync](/dapper-query/selecting-multiple-rows)
- [Read / ReadAsync](/dapper-query/selecting-multiple-results)