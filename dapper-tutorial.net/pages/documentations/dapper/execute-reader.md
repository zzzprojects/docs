---
PermaID: 1000206
Name: Execute Reader
---

# Dapper - Execute Reader

## Description

The `ExecuteReader` is an extension method that can be called from any object of type `IDbConnection`. This is typically used when the results of a query are not processed by Dapper. For example, to fill a `DataTable` or `DataSet`.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    var reader = connection.ExecuteReader("SELECT * FROM Customers;");

    DataTable table = new DataTable();
    table.Load(reader);
            
    FiddleHelper.WriteTable(table);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/BFGIVL) | [.NET Framework](https://dotnetfiddle.net/YekzFv)

### Parameters
The following table shows the different parameters of an `ExecuteReader` method.

| Name | Description |
| :--- | :---------- |
| sql            | The command text to execute. |
| param          | The command parameters (default = null). |
| transaction    | The transaction to use (default = null). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

## Example - Stored Procedure

The `ExecuteReader` extension method can also execute a stored procedure. 

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
