---
title: Dapper ExecuteScalar, ExecuteScalarAsync
description: The Dapper ExecuteScalar method allows you to execute a query and return the first column of the first row in the result set returned by the query.
canonical: /dapper-query/selecting-scalar-values
status: Published
lastmod: 2023-08-01
---

# Querying Scalar Values With Dapper

Dapper is a simple and effective tool for retrieving scalar values from databases quickly and easily. It provides an efficient API that minimizes the amount of code needed to get results from the database. 

 - With its support for parameterized queries, it can also help ensure the security of your application. 
 - If you need to quickly and easily query databases for single values or objects, Dapper is a great tool to use. 
 - It provides an efficient way to retrieve scalar values that can save you time and effort in your projects.

Dapper provides two methods for selecting scalar values along with their asynchronous counterparts.

| Method | Description | 
|:---|:---|
|`ExecuteScalar` |  Returns the first column of the first row as a `dynamic` type |
|`ExecuteScalar<T>` |  Returns the first column of the first row as the specified `T` type parameter | 
|`ExecuteScalarAsync` |  Returns the first column of the first row as a `dynamic` type asynchronously |
|`ExecuteScalarAsync<T>` |  Returns the first column of the first row as the specified `T` type parameter asynchronously  |

## Dapper ExecuteScalar

The `ExecuteScalar` method is often used to execute SQL queries that return a single row and a single column. That could include queries such as retrieving the total number of rows in a database table or the sum of values from multiple columns.

To retrieve a scalar value using Dapper, you first need to construct a query that will be executed against the database. After creating the query, you use the `ExecuteScalar` method to execute it and get back a single value from the results.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT COUNT(*) FROM Products";
    var count = connection.ExecuteScalar(sql);
	
    Console.WriteLine($"Total products: {count}");
}
```

For example, if you wanted to select the total number of records in a table, you could use a query such as `SELECT COUNT(*) FROM Products`. Then, you could use the `ExecuteScalar` method on the query to get back a single value representing the total number of records.

If you want to perform any operations on the returned value, you must cast or convert it to its expected type. Alternatively, use the version of `ExecuteScalar<T>` that takes a generic parameter and specifies the return type explicitly:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT COUNT(*) FROM Products";
    var count = connection.ExecuteScalar<int>(sql);
	
    Console.WriteLine($"Total products: {count}");
}
```

## Dapper ExecuteScalarAsync

The `ExecuteScalarAsync` is an asynchronous version of `ExecuteScalar` that executes the query asynchronously. It returns the first column of the first row from the result set returned by the query. Any additional columns or rows are ignored.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT COUNT(*) FROM Products";
    var count = await connection.ExecuteScalarAsync(sql);
	
    Console.WriteLine($"Total products: {count}");
}
```

You can also use the `ExecuteScalarAsync<T>` that takes a generic parameter and specify the return type explicitly.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "SELECT COUNT(*) FROM Products";
    var count = await connection.ExecuteScalarAsync<int>(sql);
	
    Console.WriteLine($"Total products: {count}");
}
```

## Related Articles

- [Querying](/dapper-query)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)