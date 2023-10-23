---
PermaID: 1000207
Title: Dapper Execute Scalar - Learn How to Return a Scalar Value
MetaDescription: Unlock the power of Dapper Execute Scalar to optimize your C# database operations. Learn how to use ExecuteScalar and ExecuteScalarAsync to return a strongly typed scalar value.
LastMod: 2023-10-20
---

# Dapper Execute Scalar: Discover How to Return a Scalar Value

## Description

The `ExecuteScalar` is an extension method that can be called from any object of type `IDbConnection`. 

 - It allows you to execute SQL statements or Stored Procedures and returned a scalar value on the first column of the first row in the Result Set. 
 - If the Result Set contains more than one column or row, it takes only the first column of the first row, all other values will be ignored. 
 - If the Result Set is empty it will return a `Null` reference. 
 - It is very useful to use with aggregate functions like `Count(*)` or `Sum()` etc. 


### Parameters

The `ExecuteScalar` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.

The `ExecuteScalar` method also takes optional parameters for transaction, command timeout, and command type.

The following table shows the different parameters of an `ExecuteScalar` method.

| Name | Description |
| :--- | :---------- |
| sql            | The command text to execute (sql statement or stored procedure name). |
| param          | The command parameters (default = null). |
| transaction    | The transaction to use (default = null). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

In the following example, we will use the `ExecuteScalar` method to retrieve a single value from the database. We will execute a SQL query that returns the number of rows in a table.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    int rowCount = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM Customers");

    Console.WriteLine(rowCount);
}
```

First, we created a `SqlConnection` object and then called the `ExecuteScalar` method to execute the SQL query and retrieve the number of rows in the table.

Here is another example that retrieves only the `Name` of the customer where `CustomerID` is 1. We pass the value 1 with an [anonymous type parameter](/parameter-anonymous)

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    var name = connection.ExecuteScalar<string>("SELECT Name FROM Customers WHERE CustomerID = @CustomerID;", new { CustomerID = 1});

    Console.WriteLine(name);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/JdlLM1) | [.NET Framework](https://dotnetfiddle.net/W7VwHn)
