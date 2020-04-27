---
PermaID: 1000185
Name: Transaction
---

# Dapper - Transaction

## Description
Dapper support the transaction and TransactionScope

## Transaction

Begin a new transaction from the connection and pass it in the transaction optional parameter.

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.Open();
	
	using (var transaction = connection.BeginTransaction())
	{
		var affectedRows = connection.Execute(sql, new {CustomerName = "Mark"}, transaction: transaction);
		
		transaction.Commit();
		
		Console.WriteLine(affectedRows);
	}
}
```

Try it: [.NET Core](https://dotnetfiddle.net/C5koRx) | [.NET Framework](https://dotnetfiddle.net/RlZRFz)

## TransactionScope

Begin a new transaction scope before starting the connection

```csharp
// using System.Transactions;

using (var transaction = new TransactionScope())
{
	var sql = "Invoice_Insert";

	using (var connection = My.ConnectionFactory())
	{
		connection.Open();

		var affectedRows = connection.Execute(sql,
			new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
			commandType: CommandType.StoredProcedure);
	}

	transaction.Complete();
}
```

## Dapper Transaction
`Dapper Transaction` is exactly like `Dapper` but extend the `IDbTransaction` interface instead and use `Dapper` under the hood.

It's a simple library to make it easier to work with a transaction.

Everything `Dapper` support, `Dapper Transaction` support it as well (It's only new extension method calling Dapper)

NuGet: https://www.nuget.org/packages/Dapper.Transaction/

GitHub: https://github.com/zzzprojects/Dapper.Transaction

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.Open();
	
	using (var transaction = connection.BeginTransaction())
	{
		// Dapper
		var affectedRows1 = connection.Execute(sql, new {CustomerName = "Mark"}, transaction: transaction);
		
		// Dapper Transaction
		var affectedRows2 = transaction.Execute(sql, new {CustomerName = "Mark"});

		transaction.Commit();
	}
}
```