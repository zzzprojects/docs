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

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-buffered-queries-and-how-does-it-work-in-dapper" itemprop="name">What are buffered queries and how does it work in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Buffered queries in Dapper refer to how Dapper handles the results of a query. When a query is executed in buffered mode, Dapper loads the entire result set into memory at once. That means all the data from the query is immediately available for processing, but it can consume more memory for large result sets. Buffered queries are the default behavior in Dapper.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-the-pros-of-using-buffered-queries-in-dapper" itemprop="name">What are the pros of using buffered queries in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Immediate Access**: Since all data is loaded into memory at once, it's immediately available for processing.
- **Connection Efficiency**: The database connection is closed as soon as all data is fetched, which may lead to more efficient connection use.
- **Simplicity**: Buffered queries are easier to use and handle, particularly for smaller datasets.
- **Exception Handling**: Any exceptions during data retrieval from the database will be thrown immediately during query execution.
- **Default Behavior**: It's the default mode in Dapper, so you don't need to specify any additional parameters for buffered queries.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-the-cons-of-using-buffered-queries-in-dapper" itemprop="name">What are the cons of using buffered queries in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Memory Consumption**: Buffered queries load the entire result set into memory at once, consuming a significant amount of memory for large datasets.
- **Initial Load Time**: There may be a noticeable delay before the first record is available for large result sets, as Dapper loads all the data into memory.
- **Resource Usage**: High memory usage might lead to system slowdowns or out-of-memory errors in resource-constrained environments.
- **Unsuitable for Large Data Sets**: Due to the above reasons, buffered queries may not be the best choice when dealing with very large data sets.
- **Connection Lifetime**: The connection remains open until all data is fetched, which could be problematic in the case of large result sets or slow networks.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-methods-support-buffered-queries-in-dapper" itemprop="name">Which methods support buffered queries in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

All methods support buffered queries, as this is the default behavior.
 
</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-unbuffered-queries-and-how-does-it-work-in-dapper" itemprop="name">What are unbuffered queries and how does it work in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Unbuffered queries in Dapper refer to a method of handling query results where the data is not loaded into memory all at once. Instead, each row is loaded and processed one at a time, like a stream. This method is more memory-efficient for large data sets, but the data may not be immediately available for processing like in a buffered query. You enable unbuffered queries by setting the `buffered` parameter to `false` when executing a query in Dapper.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-the-pros-of-using-unbuffered-queries-in-dapper" itemprop="name">What are the pros of using unbuffered queries in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Memory Efficiency**: Unbuffered queries load data one row at a time, which can be more memory-efficient for large result sets.
- **Better for Large Data Sets**: Because of the streaming nature of unbuffered queries, they're better suited to handling large data sets that would be impractical or impossible to load into memory all at once.
- **Lower Initial Load Time**: Unbuffered queries can start processing data as soon as the first row is available, potentially reducing the time waiting for the entire result set to load.
- **Continuous Stream**: The data is continuously streamed from the database, which can be advantageous in certain use cases.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-the-cons-of-using-unbuffered-queries-in-dapper" itemprop="name">What are the cons of using unbuffered queries in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Slower Access**: Since data is loaded one row at a time, it may take longer to access all the data compared to a buffered query where all the data is loaded into memory at once.
- **Connection Duration**: The connection to the database remains open until all data has been read, potentially keeping the connection open longer than with a buffered query, particularly with large result sets.
- **Complexity**: Handling unbuffered queries can be slightly more complex than handling buffered queries, particularly in scenarios where you need to manage the open connection for a longer period.
- **Partial Data**: If an error occurs midway through an unbuffered read, you may end up with only a portion of the expected data.
- **Memory Management**: While unbuffered queries are more memory-efficient for large data sets, for small data sets, the overhead of managing the "streaming" of data could potentially use more resources than simply loading all the data into memory.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-methods-support-unbuffered-queries-in-dapper" itemprop="name">Which methods support unbuffered queries in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

In Dapper, only two base methods support unbuffered queries:

- **`Query` / `QueryAsync`**: You can set the buffered parameter to false to the [Query / QueryAync methods](/dapper-query/selecting-multiple-rows) to use unbuffered queries.
- **`Read` / `ReadAsync`**: You can use those methods from the `GridReader` returned by [QueryMultiple / QueryMultipleAsync methods](/dapper-query/selecting-multiple-results).
 
</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="do-queries-are-buffered-or-unbuffered-by-default-in-dapper" itemprop="name">Do queries are buffered or unbuffered by default in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

In Dapper, queries are buffered by default. That means the entire result set is loaded into memory all at once when the query is executed.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-can-i-set-queries-to-be-unbuffered-by-default-in-dapper" itemprop="name">How can I set queries to be unbuffered by default in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Unfortunately, Dapper doesn't have any options to change this behavior. If you want to use an unbuffered query, you must explicitly set the `buffered` parameter to `false` when executing the query.

```csharp
var customers = connection.Query<Customer>(sql, buffered: false);
```

</div></div>
</details>

</div>

## Conclusion

Generally, when using Dapper, it's best to use buffered queries for large datasets and unbuffered queries for smaller data sets. However, depending on your application, it may also be a good idea to try out both options and see which one works best for your situation. Ultimately, the right choice depends on the particular needs of your application and how much performance optimization is required.

## Related Articles

- [Querying](/dapper-query)
- [Query / QueryAync](/dapper-query/selecting-multiple-rows)
- [Read / ReadAsync](/dapper-query/selecting-multiple-results)