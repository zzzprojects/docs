---
PermaID: 1000185
Title: Dapper Transaction - Learn How to Manage your Transaction
MetaDescription: Unlock the power of Dapper by using Transaction. Learn how to use a transaction when saving data and completing it with 'Commit' or 'Rollback'.
LastMod: 2023-10-20
---

# Dapper Transaction: Discover How to Manage your Transaction

## Description

One of the key features of Dapper is its support for managing transactions. Transactions allow you to group multiple operations into a single unit of work, which can then be committed or rolled back as a single unit. 

### Steps

Using Dapper's transaction support is very simple. 

 1. First, you create an `IDbConnection` object, which represents a connection to your database. 
 2. Next, you call `IDbConnection.BeginTransaction()` to start a new transaction. 
 3. Then, you execute all the operations that you want to be part of the transaction using this `IDbConnection` object. 
 4. Finally, you call `IDbTransaction.Commit()` or `IDbTransaction.Rollback()` to complete the transaction.

Dapper support the transaction and TransactionScope

## Transaction

Begin a new transaction from the connection and pass it in the transaction optional parameter. It can be useful when you need to make sure that multiple operations are all successful, or if you want to be able to undo all the operations as a group.

Let's take a look at a simple example. Suppose we have a database table that stores information about customers, and we want to add a new customer. 

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

We also want to make sure that the new customer's data is valid before we [insert it into the database](/execute#example-execute-insert), so we will start a transaction. If the data is valid, we will commit the transaction and insert the new customer. 

Try it: [.NET Core](https://dotnetfiddle.net/C5koRx) | [.NET Framework](https://dotnetfiddle.net/RlZRFz)

## TransactionScope

The `TransactionScope` class provides a simple way to mark a block of code as participating in a transaction, without requiring you to interact with the transaction itself.

The following example shows how to begin a new transaction scope before starting the connection.

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

`Dapper Transaction` is exactly like `Dapper` but extends the `IDbTransaction` interface instead and uses `Dapper` under the hood.

It's a simple library to make it easier to work with a transaction.

Everything `Dapper` support, `Dapper Transaction` supports it as well (It's only a new extension method calling Dapper)

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

## Related Articles

- [Dapper - Execute](/execute)