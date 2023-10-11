---
title: Dapper Execute, ExecuteAsync, Insert, Update, Delete
description:  The Dapper Execute method allows executing SQL statements not returning any results besides the row affecteds for operations such as insert, update, and delete.
canonical: /non-query
status: Published
lastmod: 2023-01-05T12:53:15Z
---

# Executing Non-Query Commands With Dapper

Dapper allows you to execute non-query commands that are not intended to return resultsets, such as `INSERT`, `UPDATE`, and `DELETE` statements. To do this, you use the `Execute` extension method of the `IDbConnection` interface, which Dapper implements.

The `Execute` method takes a few different parameters;
- One is the command text (the SQL query) to execute.
- The other is a parameter object that can be an anonymous type or even a Dapper DynamicParameters object if you need to specify additional information about the parameters to use in the query.

The `Execute` method returns an `int`, representing the number of rows affected by the successful completion of the command.

Here is an example of how to use the `Execute` method.

```csharp
string sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email);" 
object[] parameters = { new { Name = "John Doe", Email = "jdoe@example.com" } };
	
using (var connection = new SqlConnection(connectionString))
{
    connection.Execute(sql, parameters); 
}
```

## Dapper Insert

The `INSERT` statement is used to add records to a table. You can use the Dapper `Execute` method to execute an `INSERT` statement to add one or more record(s) to the database table.

```csharp
var sql = "INSERT INTO Categories (CategoryName) values ('New Category')";

using (var connection = new SqlConnection(connectionString))
{
    var affectedRows =  connection.Execute(sql);
	
    Console.WriteLine($"Affected Rows: {affectedRows}");
}
```
![Image](/images/20-05-2019-07-55-49.png)

Dapper also supports inserting a list of objects, but it is an expensive operation if you use the dapper `Execute` method. The [BulkInsert](/bulk-operations/bulk-insert) method makes it easy to manage large amounts of data efficiently. It allows you to quickly and safely insert multiple records in bulk, reducing the need for manual input or looping through individual records.

## Dapper Update

The `UPDATE` statement is used to update existing records in a table. You can use the `Execute` method of Dapper to perform an `UPDATE` statement by passing a string containing the SQL query and any needed parameters. 

```csharp
var sql = @"UPDATE Products SET TotalPrice = Quantity * UnitPrice WHERE CategoryID = 2";

using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = connection.Execute(sql);
	
    Console.WriteLine($"Affected Rows: {affectedRows}");
}
```
![Image](/images/20-05-2019-07-52-25.png)

Dapper also supports updating a list of objects. However, if you are looking for a highly efficient way to update thousands of entities, you should look at the [BulkUpdate](/bulk-operations/bulk-update) method.

## Dapper Delete

Dapper provides an `Execute` method for deleting data from the database. When using the `Execute` method for deleting data, it is essential to note that it returns an integer indicating the number of rows affected by the query. 

The value returned by the `Execute` method can be used to determine if the delete operation was successful or not. It is also important to note that any errors thrown by the database must be handled in your code. 

```csharp
var sql = "DELETE FROM Categories WHERE CategoryName = 'New Category'";

using (var connection = new SqlConnection(connectionString))
{
    var affectedRows =  connection.Execute(sql);
	
    Console.WriteLine($"Affected Rows: {affectedRows}");
}
```

![Image](/images/20-05-2019-07-55-49.png)

The [BulkDelete](/bulk-operations/bulk-update) method allows you to delete large amounts of data efficiently. By using bulk operation instead of the `Execute` method, you reduce the number of database round-trips and increase your application performance.

## Dapper ExecuteAsync

The Dapper `ExecuteAsync` method is an asynchronous version of the `Execute` command. It enables you to run a query asynchronously without waiting for it to finish before continuing your code execution. It can be helpful when dealing with large or complex queries that require additional time to complete. 

 - The async method will return a `Task` object that can be awaited or used to monitor the progress of the query. 
 - The `ExecuteAsync` method is a great way to improve performance when dealing with multiple queries. It allows you to execute them in parallel without waiting for one to finish before proceeding with the next. 
 - It can dramatically reduce overall execution times compared to running queries sequentially. 
 - As with the `Execute` command, the `ExecuteAsync` method also supports passing in parameters and can be used to execute commands against multiple databases. 

```csharp
string sql = "INSERT INTO Users (UserName) Values (@UserName);";

using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = await connection.ExecuteAsync(sql, new { UserName = "Mark" });
	
    Console.WriteLine(affectedRows);
}
```

It is important to note that while the async version of this command will generally improve performance, it should not be used for queries that need to return immediate results. In those cases, you should use the synchronous version of the Execute command. 

## Conclusion

Using the `Execute` and `ExecuteAsync` methods in Dapper, you can easily execute non-query commands with minimal code and configuration. That makes it an excellent choice for executing these kinds of commands quickly and effectively.

However, we suggest you look for [bulk operation](/bulk-operations) methods when you have to handle hundreds, thousands, or even millions of rows.

## Related Articles

- [Using Parameters](/parameters)
