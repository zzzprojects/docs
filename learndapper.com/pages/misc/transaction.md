---
title: Dapper Transaction
description: Dapper support transaction and allow you to commit/rollback the current transaction. Learn more about how to use transactions, transactions scope, and more.
canonical: /misc/transaction
status: Published
lastmod: 2023-01-05
---

# Transaction With Dapper

The transaction is a set of operations treated as a unit, and either all take effect or none. SQL Server uses a transaction log to ensure that each transaction is completed successfully and securely. 

 - The transaction log records all changes made by a transaction, including inserts, updates, deletes, and any other operations. 
 - Each time a change is made to the database, an entry is written to the transaction log with information about the operation. 
 - This information includes the time the operation was made, the user who executed it, the type of operation that occurred (insert, update, delete), and any modified data. 
 - When a transaction is started in SQL Server, all changes to the database are recorded in an "uncommitted" state until the user explicitly commits them. 
 - It allows users to roll back any changes if needed, as all of the data is still available in its original state.
 - Once a transaction has been committed, it is written to the database and can no longer be rolled back or undone. 

## Dapper Transaction

Dapper provides different ways to work with transactions. The most common way is to use the `BeginTransaction` method available on the `IDbConnection` interface. That will return an instance of `IDbTransaction`, which you can use with `Execute` and `Query` methods to add, remove and modify data in your database tables. 

 - Once you have finished executing your commands within the transaction scope, you can either commit the transaction or roll back the changes.
 - Committing the transaction will save all your changes to the database while rolling back any changes will restore the data to its previous state before you start the transaction. 
 - To commit a transaction in Dapper, you can call the `Commit` method on the `IDbTransaction` object. 
 - Similarly, you can use the `Rollback` method to undo the changes made during the Transaction.

```csharp
// Open the connection and start a transaction: 
using (var connection = new SqlConnection(connectionString))
{ 
    connection.Open();
	
    using (var tran = connection.BeginTransaction()) 
    { 
        // Execute your queries here
		
        tran.Commit(); //Or rollback 
    }
}
```

You can also use the `TransactionScope` class, which provides a much simpler way to deal with transactions: 

```csharp
// Open the connection and start a transaction: 
using (var scope = new TransactionScope()) 
{ 
    // Execute your queries here
	
    scope.Complete(); //Or it will automatically rollback 
}
```

In addition, Dapper also provides a set of asynchronous methods that provide the same functionality as their synchronous counterparts: `BeginTransactionAsync`, `CommitAsync`, and `RollbackAsync`. These methods can be used in place of the synchronous ones when performing operations asynchronously.  

Using transactions in Dapper is a powerful and straightforward way to ensure that all your database operations are performed within a single atomic transaction. Keeping this in mind, you should always make sure that you handle transactions carefully and use them only when necessary. 

## Dapper Transaction Library

One way to easily use transaction is through the Dapper.Transaction library.

This library is exactly like Dapper but extend the IDbTransaction interface instead and use Dapper under the hood.

NuGet Package: [https://www.nuget.org/packages/Dapper.Transaction/](https://www.nuget.org/packages/Dapper.Transaction/)

Documentation:

- [Website](https://dappertutorial.net/dapper-transaction)
- [GitHub](https://github.com/zzzprojects/Dapper.Transaction)

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