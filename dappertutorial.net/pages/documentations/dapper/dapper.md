---
PermaID: 1000167
Title: Dapper - Learn What is Dapper and Why You Should Use it
MetaDescription: Unlock the power of Dapper by understanding the basics and more advanced topics. Learn what Dapper is, how it works, and all the documentation you need to get started.
LastMod: 2023-10-20
---

# Dapper: Discover What is Dapper and Why You Should Use it

## What's Dapper?

Dapper is a micro-ORM created by the team behind Stack Overflow. Dapper is a simple object mapper for .NET and owns the title of **King of Micro ORM** in terms of speed and is virtually as fast as using a raw ADO.NET data reader. An ORM is an Object Relational Mapper responsible for mapping between a database and a programming language.

  - Dapper is a popular open source Object-Relational Mapping (ORM) Library for .NET.
  - It makes it easy to work with data in your application by mapping objects to tables in a database. 
  - Dapper is fast, reliable, and well-tested and has been used in production by some of the world's largest companies for many years.
  - It is very easy to use and has a lot of features that make it a powerful tool for data access. 

## Is Dapper faster than Entity Framework?

Yes, Dapper is faster than Entity Framework for CRUD operation (querying and saving) due to its simplicity. Unlike EF Core, it doesn't have to add all complexity, such as tracking values, writing inefficient SQL queries, and supporting features like lazy loading and all inheritance by default.

However, EF Core is relatively very fast as well. The question about which ORM is the best for you should be more about if you want to write most of your SQL query (Dapper) or if you prefer to write LINQ and have EF Core write the SQL query for you.

For a better comparisons, see [Dapper vs Entity Framework (EF6 or EF Core)](https://www.learndapper.com/dapper-vs-entity-framework)

## How does Dapper work?

Dapper provides a simple and concise way to manage your data model without having to write a lot of code. It is also very easy to use, and its code is clean and readable.

Dapper extends the `IDbConnection` interface by providing helpful extension methods to query your database. It uses dynamic method generation to enable it to inflate POCOs directly from query results. In addition, it allows you to map database columns directly to properties on your POCO.

When using Dapper, all you need is a connection string and a POCO, and then it is a three-step process.

- Create an `IDbConnection` object.
- Write a query to perform CRUD operations.
- Pass the query as a parameter in any [Execute](/execute) or [Query](/query) method.

## Is Dapper case sensitive or insensitive?

Dapper is case insensitive when mapping column to property. It doesn't matter if the column name returned is `CustomerID` or `customerid`. In both cases, it will be mapped to the right property, such as `CustomerId`, no matter the casing.

## Installation

It is straightforward to install Dapper, you can either download the [Dapper NuGet package](https://www.nuget.org/packages/Dapper/) or clone the [Dapper GitHub repository](https://github.com/DapperLib/Dapper). 

To install the Dapper NuGet Package, run the following command in the Package Manager Console:

```csharp
PM> NuGet\Install-Package Dapper 
```

To clone the Dapper GitHub repository, run the following command in a Git Bash window:


```csharp
git clone https://github.com/StackExchange/Dapper.git
```

## Requirements

Dapper works with any .NET project. This means that it can be used with the following frameworks:

 - .NET Core 5.0 and above
 - .NET Framework 4.6.1 and above
 - .NET Standard 2.0 and above

_(Older version is also supported by using older version of Dapper)_

Dapper also requires to add a provider package such as:

- Microsoft.Data.SqlClient (for SQL Server)
- System.Data.SqlClient (alternative for SQL Server)
- Microsoft.Data.Sqlite (for SQLite)
- Any other provider package!

Dapper works with any database provider since there is no database-specific implementation.

Once you have installed the required NuGet packages, you can start using Dapper in your project.


## Methods

Dapper extension methods can be used to perform various operations in the database, such as fetching data, inserting records, updating records, and deleting records.

Dapper will extend your `IDbConnection` interface with multiple methods and some of the most commonly used dapper extension methods.

- [Execute](/execute): It can execute a command one or multiple times.
- [ExecuteReader](/execute-reader): It can execute a command and return a reader.
- [Executescalar](/execute-scalar): It can execute a command and return a scalar value.
- [Query](/query): Used to fetch data from the database.
- [QueryFirst](/queryfirst): It can execute a query and map the first result.
- [QueryFirstOrDefault](/queryfirstordefault): It can execute a query and map the first result, or a default value if the sequence contains no elements.
- [QuerySingle](/querysingle): It can execute a query and map the first result and throws an exception if there is not exactly one element in the sequence.
- [QuerySingleOrDefault](/querysingleordefault): It can execute a query and map the first result, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.
- [QueryMultiple](/querymultiple): It can execute multiple queries within the same command and map results.

```csharp
string sqlOrderDetails = "SELECT TOP 5 * FROM OrderDetails;";
string sqlOrderDetail = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";
string sqlCustomerInsert = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetails = connection.Query<OrderDetail>(sqlOrderDetails).ToList();
    var orderDetail = connection.QueryFirstOrDefault<OrderDetail>(sqlOrderDetail, new {OrderDetailID = 1});
    var affectedRows = connection.Execute(sqlCustomerInsert,  new {CustomerName = "Mark"});

    Console.WriteLine(orderDetails.Count);
    Console.WriteLine(affectedRows);

    FiddleHelper.WriteTable(orderDetails);
    FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```

Try it: [.NET Core](https://dotnetfiddle.net/FghvFq) | [.NET Framework](https://dotnetfiddle.net/vIvUNm)

## Parameter

Dapper supports many different parameter types. Execute and queries method can use parameters from multiple different ways:

- [Anonymous](/parameter-anonymous): Useful for simple queries where you don't need to create a separate class to represent your data.
- [Dynamic](/parameter-dynamic): Useful for when you need to create a dynamic list of parameters, or when you need to dynamically change the value of a parameter.
- [List](/parameter-list): This allows you to specify multiple parameters on an `IN` clause by using a list.
- [String](/parameter-string): Useful when working with SQL Server stored procedures that accept varchar input parameters.

```csharp
// Anonymous
var affectedRows = connection.Execute(sql,
                    new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
                    commandType: CommandType.StoredProcedure);

// Dynamic
DynamicParameters parameter = new DynamicParameters();

parameter.Add("@Kind", InvoiceKind.WebInvoice, DbType.Int32, ParameterDirection.Input);
parameter.Add("@Code", "Many_Insert_0", DbType.String, ParameterDirection.Input);
parameter.Add("@RowCount", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

connection.Execute(sql,
    parameter,
    commandType: CommandType.StoredProcedure);

int rowCount = parameter.Get<int>("@RowCount");

// List
connection.Query<Invoice>(sql, new {Kind = new[] {InvoiceKind.StoreInvoice, InvoiceKind.WebInvoice}}).ToList();
// String
connection.Query<Invoice>(sql, new {Code = new DbString {Value = "Invoice_1", IsFixedLength = false, Length = 9, IsAnsi = true}}).ToList();
```

## Result

When using Dapper, you can map your query results to objects in several ways. The result returned by the queries method can be mapped to multiple types:

- [Anonymous](/result-anonymous): Allow you to store the results of a SQL query in an anonymous type.
- [Strongly Typed](/result-strongly-typed): Allow you to store the results of a SQL query in a strongly typed manner.
- [Multi-Mapping](/result-multi-mapping): Map the result to a strongly typed list with relations.
- [Multi-Result](/result-multi-result): Execute multiple queries within the same command and map results.
- [Multi-Type](/result-multi-type): Execute a query and map the result to a list of different types.

```csharp
string sqlOrderDetails = "SELECT TOP 10 * FROM OrderDetails;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var anonymousList = connection.Query(sqlOrderDetails).ToList();
    var orderDetails = connection.Query<OrderDetail>(sqlOrderDetails).ToList();

    Console.WriteLine(anonymousList.Count);
    Console.WriteLine(orderDetails.Count);

    FiddleHelper.WriteTable(orderDetails);

    FiddleHelper.WriteTable(connection.Query(sqlOrderDetails).FirstOrDefault());
}
```

Try it: [.NET Core](https://dotnetfiddle.net/VPwBKR) | [.NET Framework](https://dotnetfiddle.net/EbR9BP)

## Utilities

In addition, to the ability to execute multiple statements in a single round trip to the server, Dapper also provides some additional functionalities that can greatly improve performance in some scenarios. 

- [Async](async): Dapper also provides an Async (asynchronous) version of extension methods
- [Buffered](buffered): A buffered query return the entire reader at once
- [Transaction](transaction): Support the transaction and TransactionScope
- [Stored Procedure](stored-procedure): Has built-in support for caching stored procedures.

```csharp
// Async
connection.QueryAsync<Invoice>(sql)

// Buffered
connection.Query<Invoice>(sql, buffered: false)

// Transaction
using (var transaction = connection.BeginTransaction())
{
    var affectedRows = connection.Execute(sql,
    new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
    commandType: CommandType.StoredProcedure,
    transaction: transaction);

    transaction.Commit();
}

// Stored Procedure
var affectedRows = connection.Execute(sql,
new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
commandType: CommandType.StoredProcedure);
```