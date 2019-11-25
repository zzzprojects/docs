---
PermaID: 1000207
Name: Execute Scalar 
---

# Dapper - Execute Scalar 

## Description

The `ExecuteScalar ` is an extension method that can be called from any object of type `IDbConnection`. It executes the query, and returns the first column of the first row in the result set returned by the query. The additional columns or rows are ignored.

### Parameters

The following table shows the different parameters of an `ExecuteScalar` method.

| Name | Description |
| :--- | :---------- |
| sql            | The command text to execute. |
| param          | The command parameters (default = null). |
| transaction    | The transaction to use (default = null). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    var name = connection.ExecuteScalar<string>("SELECT Name FROM Customers WHERE CustomerID = 1;");

    Console.WriteLine(name);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/JdlLM1) | [.NET Framework](https://dotnetfiddle.net/W7VwHn)
