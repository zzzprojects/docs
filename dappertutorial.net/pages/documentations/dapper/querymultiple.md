---
PermaID: 1000176
Title: Dapper Multiple - Learn How to Return Multiple Results by Batching
MetaDescription: Unlock the power of Dapper Query Multiple to optimize your C# database operations. Learn how to use QueryMultiple and QueryMultipleAsync to return a GridReader and use it to return an anonymous type, strongly type entities, or a scalar value.
LastMod: 2023-10-20
---

# Dapper Multiple: Discover How to Return Multiple Results by Batching SQL

## Description

The `QueryMultiple` is an extension method of the `IDbConnection` interface. It executes multiple queries within the same command and maps the results to strong entities. 

### Parameters

The syntax of the `QueryMultiple` method is given below:

```csharp
GridReader QueryMultiple(string sql, 
                         object param = null, 
                         IDbTransaction transaction = null, 
                         int? commandTimeout = null, 
                         CommandType? commandType = null);
```

 - The `QueryMultiple` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.
 - The `QueryMultiple` method also takes optional parameters for transaction, command timeout, and command type.
 - The return type of `QueryMultiple` is an object that implements `IDataReader`. We can use this object to read the results of each SQL statement.

The following table shows the different parameters of a `QueryMultiple` method.

| Name | Description |
| :--- | :---------- |
| sql            | It represents an SQL query or stored procedure. This parameter is required. |
| param          | It represents the parameters required by SQL query or stored procedure. This parameter is optional. We can pass the parameter to SQL in anonymous type, `dynamic` object, or `Dapper.DynamicParameters` class. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |
| commandType    | It specifies how SQL query or stored procedure should be interpreted by the data provider. The default value of this parameter is Text. This parameter is optional. |

The following example shows how to execute two SQL statements in one round trip to the database. In the first query, we get all invoices from the `Invoices` table, and in the second query, we get all invoice items from the `InvoiceItems` table. 


```csharp
string sql = "SELECT * FROM Invoices WHERE InvoiceID = @InvoiceID; SELECT * FROM InvoiceItems WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    using (var multi = connection.QueryMultiple(sql, new {InvoiceID = 1}))
    {
        var invoice = multi.Read<Invoice>().First();
        var invoiceItems = multi.Read<InvoiceItem>().ToList();
    }
}
```

We map the results of both queries to the `Invoice` and `InvoiceItem` classes.
