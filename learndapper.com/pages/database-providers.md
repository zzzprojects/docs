---
title: Dapper Database Providers - SQL Server - Oracle - MySQL - PostgreSQL
description: Discover how to use Dapper database providers like SQL Server, MySQL, PostgreSQL, Oracle, and more on your website. Learn to install and apply these providers.
canonical: /database-providers
status: Published
lastmod: 2023-08-01
---

# Database Providers With Dapper

Dapper supports a variety of database providers, including the popular Microsoft SQL Server, Oracle, MySQL, PostgreSQL, SQLite, etc. 

 - It is important to note that Dapper does not provide any database abstraction layer like Entity Framework or NHibernate. 
 - Instead, it provides the user with several methods and extension points to extend the library for their custom database provider. 
 - This is possible because it uses the simple ADO.NET data providers that come with the .NET framework, so it can easily work with any existing database technology that supports ADO.NET. 
 - Dapper provides asynchronous support for all databases supported in ADO.NET. 
 - It also provides a range of powerful features, such as parameterized queries and native commands for all major database engines.

## Dapper SQL Server

Using Dapper to query SQL Server is straightforward. The first step is to install [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet package. You can also do it through the Nuget Package Manager Console by typing the following command.

```csharp
PM> NuGet\Install-Package Microsoft.Data.SqlClient
``` 

Using Dapper to access SQL Server is relatively easy. You first create a connection and start using methods from Dapper available.

```csharp
// Connect to the database 
using (var connection = new SqlConnection(connectionString)) 
{    
    // Create a query that retrieves all books with an author name of "John Smith"    
    var sql = "SELECT * FROM Books WHERE Author = @authorName";     
	
    // Use the Query method to execute the query and return a list of objects    
    var books = connection.Query<Book>(sql, new { authorName = "John Smith"}).ToList(); 
}
```

Once you have created the connection, you can use any [Dapper Query](/dapper-query/selecting-multiple-rows#dapper-query) methods to map the data returned from your query into a strongly typed object (or any other supported [querying methods](/dapper-query)). In addition, it includes [executing stored procedures](/stored-procedures) to retrieve data from the database.

## Dapper Oracle

To start using Oracle with Dapper, you must install the [Oracle.ManagedDataAccess.Core](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core) NuGet package.

Once the connection is created, you can begin writing code that uses Dapper's built-in methods for interacting with the database. These include [Execute](/non-query), [Execute Reader](/execute-reader), etc.

```csharp
// Connect to the database 
using (var connection = new OracleConnection(connectionString)) 
{    
    // Create a query that retrieves all books with an author name of "John Smith"    
    var sql = "SELECT * FROM Books WHERE Author=@authorName";     

    // Use the Query method to execute the query and return a list of objects    
    var books = connection.Query<Book>(sql, new {authorName="John Smith"}).ToList(); 
}
```

## Dapper SQLite

Dapper offers an easy way to access and manipulate data in an SQLite database. With its simple API and powerful features, Dapper can significantly reduce the amount of code needed to perform these tasks. As a result, it is an excellent choice for working with small datasets or prototyping applications. 

To start using SQLite with Dapper, you must install the [System.Data.SQLite.Core](https://www.nuget.org/packages/System.Data.SQLite.Core) NuGet package.


Using Dapper, you can connect to an SQLite database using a connection string like this: 

```csharp
private static string connectionString = "Data Source=D:\\BookStoreContext.db;";
```

To execute any SQL statement, such as returning the first author found by using [QueryFirst](/dapper-query/selecting-single-rows#dapper-queryfirst) or [QuerySingle](/dapper-query/selecting-single-rows) if only a single row is returned.

```csharp
// Connect to the database
using (var connection = new SQLiteConnection(connectionString))
{
    // Create a query that retrieves all authors"    
    var sql = "SELECT * FROM Authors LIMIT 1;";     

    // Use the Query method to execute the query and return the first author
    var author = connection.QueryFirst<Author>(sql).
}
```

## Dapper MySql

When working with MySQL in .NET Core, you must install the following NuGet package [MySql.Data](https://www.nuget.org/packages/MySql.Data).

Using Dapper to retrieve data from MySql is simple and can be done in a few lines of code. First, create a connection, then use any query method provided by Dapper, such as [ExecuteScalar](/dapper-query/selecting-scalar-values).

```csharp
// Connect to the database
using (var connection = new MySqlConnection(connectionString))
{
    // Create a query that retrieves all authors"    
    var sql = "SELECT * FROM Authors";     

    // Use the Query method to execute the query and return a list of objects
    List<Author> authors = connection.Query<Author>(sql).ToList();
}
```

## Dapper PostgreSQL

Dapper enables developers to easily and quickly query their PostgreSQL databases without writing complex SQL statements. 

 - With Dapper, developers can easily map data from Postgres tables into .NET objects in minutes.
 - This makes retrieving, storing, and manipulating data in PostgreSQL incredibly simple.

To start using PostgreSQL with Dapper, you must install the [Npgsql](https://www.nuget.org/packages/Npgsql) NuGet package.


```csharp
// Connect to the database
using (var connection = new NpgsqlConnection(connectionString))
{
    // Create a query that retrieves all authors"    
    var sql = "SELECT * FROM Authors";     

    // Use the Query method to execute the query and return a list of objects
    List<Author> authors = connection.Query<Author>(sql).ToList();
}
```

Like other providers, you must first create a connection and use any available Dapper methods, including [Dapper Bulk Operations](/bulk-operations) methods.

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-database-providers-is-supported-in-dapper" itemprop="name">What database providers is supported in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Dapper supports a wide range of database providers that are compatible with ADO.NET, including but not limited to Microsoft SQL Server, MySQL, PostgreSQL, Oracle, and SQLite.

Since Dapper has no database specific implementation, it works with pretty much all providers.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-can-i-install-a-database-provider-in-dapper" itemprop="name">How do I install a database provider in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To install a database provider in Dapper, you need to add the appropriate [NuGet](https://www.nuget.org/) package to your project. For example:

- **SQL Server**: You need to install the [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) package
- **Oracle**: You need to install the [Oracle.ManagedDataAccess.Core](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core) package
- **MySQL**: You need to install the [MySql.Data](https://www.nuget.org/packages/MySql.Data) package
- **PostgreSQL**: You need install the [Npgsql](https://www.nuget.org/packages/Npgsql) package
- **SQLite**: You need to install the [System.Data.SQLite.Core](https://www.nuget.org/packages/System.Data.SQLite.Core) package

More than one package might exist for the same providers. We just have included the most popular one.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="is-ado-net-provider-supported-in-dapper" itemprop="name">Is ADO.NET provider supported in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Yes, Dapper is built on top of ADO.NET and supports all databases that have an ADO.NET provider. That allows Dapper to work with a wide range of database technologies, including SQL Server, MySQL, PostgreSQL, Oracle, SQLite, and more.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="are-there-any-performance-considerations-when-using-a-specific-database-provider-in-dapper" itemprop="name">Are there any performance considerations when using a specific database provider in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Dapper is designed to be very efficient, but actual performance can depend on several factors, such as the specific database provider, the complexity and efficiency of your queries, the size of the result set, and the load on the database server. Always ensure your SQL queries are optimized for the best performance.

</div></div>
</details>

</div>

## Related Articles

- [Bulk Operations](/bulk-operations)