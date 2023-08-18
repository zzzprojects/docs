---
title: Dapper Temporary Table
description: Dapper supports temporary tables by allowing you to create, insert, and query them using the execute or querying methods. Learn more how to use temporary table
canonical: /misc/temporary-table
status: Published
lastmod: 2023-01-05
---

# Temporary Table With Dapper

Temporary tables are a vital part of SQL Server. They enable database developers to store and manipulate data within the database without having to create permanent tables in the system. 

 - Temporary tables are created in memory, meaning they exist only for a certain period of time until they are dropped, or the session ends. 
 - These tables provide a great way to manipulate data within the database, but they also have some limitations that must be considered when using them. 



Temporary tables in Dapper are a great way to store data temporarily while working in your database. They allow you to quickly create and drop tables and move data between permanent and temporary tables. 

 - Temporary tables can be used for tasks such as creating multiple versions of the same table or staging data before inserting it into the actual table. 
 - They can also be used as an alternative to a view when you need to join tables but don't want the overhead of creating a permanent object. 
 - One of the key benefits of using temporary tables in Dapper is that they are only visible within your current session, so other users will not be able to see them or access the data in them. 
 - This provides a layer of security when working with sensitive data. 
 - Temporary tables are also highly efficient in terms of time and resources since they only exist as long as the session that created them. 

## Dapper Temporary Table

The syntax for creating a temporary table in SQL Server is as follows:

```csharp
CREATE TABLE #TempTableName
( 
    Column1 datatype, 
    Column2 datatype, 
    ... 
); 
```
 
In the above syntax, `#TempTableName` is the name of the temporary table, and `Column1` and `Column2` are the names of two columns in the table. The `datatype` can be any valid SQL Server data type such as `int`, `varchar`, or `nvarchar`. 

Once you have created your temporary table, you can add data using the `INSERT` statement. This statement will allow you to populate your table with whatever data you need for the duration of your connection. 

```csharp
using (var connection = new SqlConnection(connectionString))
{
    await conn.Open();
	
    connection.Execute(@"CREATE TABLE #tmpOrder(orderId int);");
    connection.Execute(@"INSERT INTO #tmpOrder(orderId) VALUES (1);");
	
    return connection.Query<int>(@"SELECT * FROM #tmpOrder;");
}
```

You can also query a temporary table like any other table in the database; however, all changes made to it will be lost after closing the connection. Temporary tables can be a great way to store data temporarily without creating permanent tables in the database. 
