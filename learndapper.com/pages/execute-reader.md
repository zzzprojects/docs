---
title: Dapper ExecuteReader, ExecuteReaderAsync
description: The Dapper ExecuteReader method allows to executes a SQL query and returns a DataReader containing the result set of the query as rows and columns. 
canonical: /execute-reader
status: Published
lastmod: 2023-01-05
---

# Executing Reader With Dapper

The `ExecuteReader` method of the Dapper library is a versatile method you can use to execute SQL statements and map the results to an instance of type `IDataReader`. That enables developers to easily read data from databases in applications written using C#, VB.NET, or other .NET languages. 

The `ExecuteReader` method has a variety of overloads that allow developers to specify the command type, parameters, transaction, and command timeout. The following table shows the different parameters of an `ExecuteReader` method.

| Name | Description |
| :--- | :---------- |
| sql            | It represents an SQL query or stored procedure. This parameter is required. |
| param          | It represents the parameters required by SQL query or stored procedure. This parameter is optional. We can pass the parameter to SQL in anonymous type, `dynamic` object, or `Dapper.DynamicParameters` class. |
| transaction    | It represents a database transaction. This parameter is optional if we use this method outside of a transaction. Otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |
| commandType    | It specifies how SQL query or stored procedure should be interpreted by the data provider. The default value of this parameter is Text. This parameter is optional. |

## Dapper ExecuteReader

The following example shows how to use the `ExecuteReader` method. First, we create a connection to the database using a `SqlConnection` object and then create an SQL query to select the data from the database and pass it to the `ExecuteReader` method of Dapper.

```csharp
using(var connection = new SqlConnection(connectionString))
{
    var reader = connection.ExecuteReader("SELECT * FROM Customers;");
	
    while (reader.Read())
    {
        int id = reader.GetInt32(0);  // Get the first column of the row as an int
        string name = reader.GetString(1);  // Get the second column of the row as a string    
    
        Console.WriteLine("Id: {0}, Name: {1}", id, name);
    
    }
}
```

You can also fill a `DataTable` or `DataSet` from the results returned by the `ExecuteReader` method. 

```csharp
using(var connection = new SqlConnection(connectionString))
{
    var reader = connection.ExecuteReader("SELECT * FROM Customers;");
	
    DataTable table = new DataTable();
    table.Load(reader);
}
```

## Dapper ExecuteReaderAsync

To execute a reader asynchronously, Dapper provides the `ExecuteReaderAsync` method, an asynchronous version of the `ExecuteReader` method.

```csharp
using(var connection = new SqlConnection(connectionString))
{
    var reader = await connection.ExecuteReaderAsync("SELECT * FROM Customers;");
	
    while (reader.Read())
    {
        int id = reader.GetInt32(0);  // Get the first column of the row as an int
        string name = reader.GetString(1);  // Get the second column of the row as a string    
		
        Console.WriteLine("Id: {0}, Name: {1}", id, name);
    }
}
```

## Related Articles

- [Using Parameters](/parameters)
