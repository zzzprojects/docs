---
title: Dapper QueryMultiple, QueryMultipleAsync
description: The Dapper QueryMultiple method allows you to select multiple results from a database in a single query and then, after reading those results for mapping them.
canonical: /dapper-query/selecting-multiple-results
status: Published
lastmod: 2023-08-01
---

# Querying Multiple Results With Dapper

You can execute multiple queries in a single SQL statement using the `QueryMultiple` method (if the [database provider](/database-providers) supports it). Once executed, you can map the returned results to multiple objects using methods such as `Read<T>`, `ReadFirst<T>`, `ReadSingle<T>` and more.

The Dapper `QueryMultiple` method allows you to select multiple results from a database query. That feature is very useful for selecting multiple results sets at once, thus avoiding unnecessary round trips to the database server. 

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
SELECT * FROM Invoices WHERE InvoiceID = @InvoiceID;
SELECT * FROM InvoiceItems WHERE InvoiceID = @InvoiceID;
";

using (var connection = new SqlConnection(connectionString))
{
    using (var multi = connection.QueryMultiple(sql, new {InvoiceID = 1}))
    {
        var invoice = multi.First<Invoice>();
        var invoiceItems = multi.Read<InvoiceItem>().ToList();
    }
}
```

With this, we can execute multiple SQL statements in one query and read each result set from the `GridReader` object returned by the `QueryMultiple` method. That can help increase the performance of your application in cases where multiple queries need to be executed and their results combined. 

Using Dapper `QueryMultiple` is a great way to simplify data access and ensure that your application runs quickly and efficiently. With its easy-to-use API, you can quickly set up complex queries and access the data you need. 

## Dapper QueryMultipleAsync

The asynchronous version of the `QueryMultiple<T>`  method is `QueryMultipleAsync<T>`:

```csharp
string sql = @"
SELECT * FROM Invoices WHERE InvoiceID = @InvoiceID;
SELECT * FROM InvoiceItems WHERE InvoiceID = @InvoiceID;
";

using (var connection = new SqlConnection(connectionString))
{
    using (var multi = await connection.QueryMultipleAsync(sql, new {InvoiceID = 1}))
    {
        var invoice = await multi.FirstAsync<Invoice>();
        var invoiceItems = await multi.ReadAsync<InvoiceItem>().ToList();
    }
}
```

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)
- [Buffered & Unbuffered Queries](/misc/buffered-unbuffered)