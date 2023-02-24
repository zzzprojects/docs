---
PermaID: 1000184
Name: Stored Procedure
LastMod: 2023-02-24
---

# Dapper - Stored Procedure 

## Description

When using Dapper the standard way to call a stored procedure is via the `Query` method. However, there are times when you might want more control over the execution of the stored procedure. For example, if you want to execute a stored procedure that doesn't return any results. In this case, you can use the `Execute` method.

Using Stored Procedure in Dapper is very easy, you simply need to specify the command type.

You can use the `Execute` extension method to execute a stored procedure. To execute a stored procedure the `commandTyp` parameter must be set to `commandType:CommandType.StoredProcedure`

```csharp
var sql = "usp_UpdateTable";

using (var connection = new SqlConnection(connectionString))
{
    var rowsAffected = connection.Execute(sql, new { id = 1, value1 = "ABC", value2 = "DEF" }, commandType: CommandType.StoredProcedure);
}
```

In the example above, we are calling a stored procedure named "usp_UpdateTable" and passing in three parameters. The first parameter is an `id`, the second parameter is a `value1`, and the third parameter is a `value2`. The Execute method will return the number of rows that were updated.

### Execute Single

The `Execute` method allows you to execute the Stored Procedure a single time.

```csharp
var sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var affectedRows = connection.Execute(sql,
		new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
		commandType: CommandType.StoredProcedure);

	My.Result.Show(affectedRows);
}
```

In the above example, we are calling a stored procedure named "Invoice_Insert" a single time and passing in two parameters. 

### Execute Many

The `Execute` method also allows you to execute the Stored Procedure multiple times. Once for every object in the array list.

```csharp
var sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

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

## How to call stored procedure using dapper in C#?

Calling a stored procedure is quite simple in Dapper, as you only need to use `Query` or `Execute` methods and specify the commandType to `CommandType.StoredProcedure`.