---
PermaID: 1000206
Title: Dapper Execute Reader - Learn How to Execute SQL to Return a Reader
MetaDescription: Unlock the power of Dapper Execute Reader to optimize your C# database operations. Learn how to use ExecuteReader and ExecuteReaderAsync to return a reader and create a DataTable.
LastMod: 2023-10-20
---

# Dapper Execute Reader: Discover How to Execute SQL to Return a Reader

## Description

Dapper `ExecuteReader` is an extension method that executes a query and returns the results as a list of dynamic objects. 

 - To use this method, simply pass in the SQL query as a string and an array of parameters that will be used in the query. 
 - It returns the results as a `System.Data.Common.DbDataReader` instance. 
 - You can use this instance to access the returned rows just like you would with a regular `System.Data.Common.DbDataReader` instance. 

The `ExecuteReader` method can be called from any object of type `IDbConnection`. This is typically used when the results of a query are not processed by Dapper. For example, to fill a `DataTable` or `DataSet`.

Here is an example of how to use the Dapper `ExecuteReader` method with an [anonymous parameter](/parameter-anonymous): 

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    var reader = connection.ExecuteReader("SELECT * FROM Customers WHERE CreatedDate > @CreatedDate;", new { createdDate} );

    DataTable table = new DataTable();
    table.Load(reader);
            
    FiddleHelper.WriteTable(table);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/BFGIVL) | [.NET Framework](https://dotnetfiddle.net/YekzFv)

As you can see, Dapper `ExecuteReader` is a very simple and easy-to-use method that can be used to execute any SQL query.

### Parameters

The `ExecuteReader` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.

The `ExecuteReader` method also takes optional parameters for transaction, command timeout, and command type.

The following table shows the different parameters of an `ExecuteReader` method.

| Name | Description |
| :--- | :---------- |
| sql            | It represents an SQL query or stored procedure. This parameter is required. |
| param          | It represents the parameters required by SQL query or stored procedure. This parameter is optional. We can pass the parameter to SQL in anonymous type, `dynamic` object, or `Dapper.DynamicParameters` class. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |
| commandType    | It specifies how SQL query or stored procedure should be interpreted by the data provider. The default value of this parameter is Text. This parameter is optional. |


## Example - Stored Procedure

The `ExecuteReader` extension method can also execute a stored procedure. The following example shows how to use the `ExecuteReader` method in Dapper to execute stored procedures and map the results to strong types.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    var reader = connection.ExecuteReader("SelectAllCustomers @IsActive = @isActive", new {IsActive = true} );

    DataTable table = new DataTable();
    table.Load(reader);
            
    FiddleHelper.WriteTable(table);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/c22ZGl) | [.NET Framework](https://dotnetfiddle.net/7TnXxm)

## Related Articles

- [Dapper - Anonymous Type Parameter](/parameter-anonymous)
