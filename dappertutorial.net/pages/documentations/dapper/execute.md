---
PermaID: 1000168
Title: Dapper Execute - Learn How to Execute Stored Procedure and SQL
MetaDescription: Unlock the power of Dapper Execute to optimize your C# database operations. Learn how to use Execute and ExecuteAsync with a stored procedure, insert, update, and delete SQL statements.
LastMod: 2023-10-20
---

# Dapper Execute: Discover How to Execute Stored Procedure and SQL

## Description

Dapper has an `Execute` extension method that can be used to execute a query or stored procedure. This method returns an `int` that represents the number of rows affected by the query/stored procedure. The `Execute` method can be called from any object of type `IDbConnection`.

The `Execute` method takes several parameters and the first two parameters are the most important.

 - The first parameter is the sql statement or stored procedure name
 - The second parameter is an object that represents the parameters that will be passed to the query/stored procedure

Here is an example of how to use the `Execute` method.

```csharp
var sql = "UPDATE Table SET Column1 = value1, Column2 = value2 WHERE Id = @id";

using (var connection = new SqlConnection(connectionString))
{
    var rowsAffected = connection.Execute(sql, new { id = 1 });
}
```

In the example above, we are updating two columns in a table where the `id` is equal to "1". The `Execute` method will return the number of rows that were updated.

You can also use the `Execute` method to execute a query.

```csharp
var sql = "SELECT * FROM Table WHERE Column1 = value1";

using (var connection = new SqlConnection(connectionString))
{
    var rowsAffected = connection.Execute(sql);
}
```

In the example above, we are running a `SELECT` query to get all rows from a table where `Column1` is equal to "value1". It will return the number of rows that were returned from the query.

It can execute a command one or multiple times and return the number of affected rows. This method is usually used to execute:

- [Stored Procedure](#example-execute-stored-procedure)
- [INSERT statement](#example-execute-insert)
- [UPDATE statement](#example-execute-update)
- [DELETE statement](#example-execute-delete)

### Parameters

The `Execute` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.

The `Execute` method also takes optional parameters for transaction, command timeout, and command type.

The following table shows the different parameters of an Execute method.

| Name | Description |
| :--- | :---------- |
| sql            | The command text to execute (sql statement or stored procedure name). |
| param          | The command parameters (default = null). |
| transaction    | The transaction to use (default = null). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

## Example - Execute Stored Procedure

You can use the `Execute` extension method to execute a stored procedure. To execute a stored procedure the `commandType` parameter must be set to `commandType:CommandType.StoredProcedure`

```csharp
var sql = "usp_UpdateTable";

using (var connection = new SqlConnection(connectionString))
{
    var rowsAffected = connection.Execute(sql, new { id = 1, value1 = "ABC", value2 = "DEF" }, commandType: CommandType.StoredProcedure);
}
```

In the example above, we are calling a stored procedure named "usp_UpdateTable" and passing in three parameters. The first parameter is an `id`, the second parameter is a `value1`, and the third parameter is a `value2`. The Execute method will return the number of rows that were updated.

We also [passed parameter with anonymous type](/parameter-anonymous)

### Single

The `Execute` method allows you to execute the Stored Procedure a single time.

```csharp
string sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
    var affectedRows = connection.Execute(sql,
        new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
        commandType: CommandType.StoredProcedure);

    My.Result.Show(affectedRows);
}
```

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dappertutorial.net/images/3-anonynous-entity.png" alt="Stored Procedure Single" />

In the above example, we are calling a stored procedure named "Invoice_Insert" a single time and passing in two parameters. 

### Many

The `Execute` method also allows you to execute the Stored Procedure multiple times. Once for every object in the array list.

```csharp
string sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
    var affectedRows = connection.Execute(sql,
        new[]
        {
            new {Kind = InvoiceKind.WebInvoice, Code = "Many_Insert_1"},
            new {Kind = InvoiceKind.WebInvoice, Code = "Many_Insert_2"},
            new {Kind = InvoiceKind.StoreInvoice, Code = "Many_Insert_3"}
        },
        commandType: CommandType.StoredProcedure
    );

    My.Result.Show(affectedRows);
}
```

In the above example, a stored procedure named "Invoice_Insert" will be called three times. 

In this example, we also [passed parameter to Dapper with a List](/parameter-anonymous#many)

## Example - Execute INSERT

Using the Dapper `Execute` method, you can insert data into the database. It takes an SQL statement and an optional parameters object. This will execute the SQL statement against the database and return the number of rows affected. 

See also [Dapper - BulkInsert Method](https://dappertutorial.net/bulk-insert)

### Single

When you need to insert a single record, you can pass the `INSERT` statement directly to the `Execute` method. It also allows you to use parameterized `INSERT` statement as shown in the below example.

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var affectedRows = connection.Execute(sql, new {CustomerName = "Mark"});

    Console.WriteLine(affectedRows);

    var customer = connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerName = 'Mark'").ToList();

    FiddleHelper.WriteTable(customer);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/nLqtS0) | [.NET Framework](https://dotnetfiddle.net/P2uw27)

### Many

If you need to insert multiple records into the database then you can use the `Execute` method with a SQL statement that uses a parameterized insert statement. This will execute the `INSERT` statement for each record in the parameters object. 

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.Open();

    var affectedRows = connection.Execute(sql,
    new[]
    {
    new {CustomerName = "John"},
    new {CustomerName = "Andy"},
    new {CustomerName = "Allan"}
    }
);

Console.WriteLine(affectedRows);
```
Try it: [.NET Core](https://dotnetfiddle.net/Sfuqkx) | [.NET Framework](https://dotnetfiddle.net/vHOVx6)

## Example - Execute UPDATE

To execute an UPDATE statement using Dapper, you will need to pass in the object that you wish to update along with the SQL query. For example, if we had a `Person` class with the properties `FirstName` and `LastName`, we could update a record as follows:

```csharp
var person = new Person { FirstName = "John", LastName = "Doe" }; 

using (var connection = new SqlConnection(connectionString))
{
    connection.Execute("UPDATE People SET FirstName = @FirstName, LastName = @LastName WHERE Id = 1", person);
}
```

In this example, we are using named parameters in our SQL query. Dapper will automatically map the properties of the `Person` class to the parameters in the query.

If you are not using named parameters, you will need to pass in an anonymous object with the properties in the same order as they appear in the query. For example:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Execute("UPDATE People SET FirstName = @FirstName, LastName = @LastName WHERE Id = 1", new { FirstName = "John", LastName = "Doe" });
}
```

See also [Dapper - BulkUpdate Method](https://dappertutorial.net/bulk-update)

### Single

When you need to update a single record, you can pass the `UPDATE` statement directly to the `Execute` method.  It also allows you to use parameterized `UPDATE` statement as shown in the below example.

```csharp
string sql = "UPDATE Categories SET Description = @Description WHERE CategoryID = @CategoryID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var affectedRows = connection.Execute(sql,new {CategoryID = 1, Description = "Soft drinks, coffees, teas, beers, mixed drinks, and ales"});

    Console.WriteLine(affectedRows);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/yDvsoS) | [.NET Framework](https://dotnetfiddle.net/CWdH6z)

### Many

If you need to update multiple records in the database then you can use the `Execute` method with a SQL statement that uses a parameterized `UPDATE` statement. This will execute the `UPDATE` statement for each record in the parameters object. 

```csharp
string sql = "UPDATE Categories SET Description = @Description WHERE CategoryID = @CategoryID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    var affectedRows = connection.Execute(sql,
    new[]
    {
    new {CategoryID = 1, Description = "Soft drinks, coffees, teas, beers, mixed drinks, and ales"},
    new {CategoryID = 4, Description = "Cheeses and butters etc."}
    }
);

Console.WriteLine(affectedRows);
```

In the above example, it will update two records with `CategoryID` equal to "1" and "4".

Try it: [.NET Core]() | [.NET Framework](https://dotnetfiddle.net/qCdKI3)

## Example - Execute DELETE

You can use the `Execute` method to execute the `DELETE` statement. You will need to pass the `id` of the record to be deleted as a parameter to the SQL query.

See also [Dapper - BulkDelete Method](https://dappertutorial.net/bulk-delete)

### Single

When you need to delete a single record, you can pass the `DELETE` statement directly to the `Execute` method.  It also allows you to use parameterized `DELETE` statement as shown in the below example.

```csharp
string sql = "DELETE FROM Customers WHERE CustomerID = @CustomerID";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var affectedRows = connection.Execute(sql, new {CustomerID = 1});

    Console.WriteLine(affectedRows);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/yk3cDO) | [.NET Framework](https://dotnetfiddle.net/4bFT32)

### Many

If you need to delete multiple records from the database then you can use the `Execute` method with a SQL statement that uses a parameterized `DELETE` statement. This will execute the `DELETE` statement for each record in the parameters object. 

```csharp
string sql = "DELETE FROM OrderDetails WHERE OrderDetailID = @OrderDetailID";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var affectedRows = connection.Execute(sql, 
        new[]
    {
    new {OrderDetailID = 1},
    new {OrderDetailID = 2},
    new {OrderDetailID = 3}
    }
);

Console.WriteLine(affectedRows);
```

Try it: [.NET Core](https://dotnetfiddle.net/2igFqM) | [.NET Framework](https://dotnetfiddle.net/nxP1vL)

## Related Articles

- [Dapper - Anonymous Type Parameter](/parameter-anonymous)
- [Dapper - Anonymous Type Many](/parameter-anonymous#many)
- [Dapper - BulkInsert Method](https://dappertutorial.net/bulk-insert)
- [Dapper - BulkUpdate Method](https://dappertutorial.net/bulk-update)
- [Dapper - BulkDelete Method](https://dappertutorial.net/bulk-delete)