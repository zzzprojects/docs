---
Title: Dapper.Transaction - Learn What Is This Library and How to Use It
MetaDescription: Unlock the power of Dapper.Transaction by understanding the basics and more advanced topics of this third-party library. Learn what Dapper.Transaction is, how it works, and all the documentation you need to get started.
LastMod: 2023-10-21
---

# Dapper.Transaction: Discover What Is This Library and How to Use It

## What's Dapper Transaction?

`Dapper Transaction` is exactly like `Dapper` but extends the `IDbTransaction` interface instead and uses `Dapper` under the hood.

 - **Dapper.Transaction** is a library that provides an easy way to manage transactions in .NET applications. 
 - Transactions are important when working with databases because they allow you to keep your data consistent and avoid data corruption. 
 - The **Dapper.Transaction** library makes it easy to work with transactions by providing a simple API that can be used in your code.


Everything `Dapper` support, `Dapper Transaction` supports it as well (It's only a new extension method calling Dapper)

NuGet: https://www.nuget.org/packages/Dapper.Transaction/

NuGet (StrongName): https://www.nuget.org/packages/Dapper.Transaction.StrongName/

GitHub: https://github.com/zzzprojects/Dapper.Transaction

## Method Supported

`Dapper Transaction` supports all the methods that are supported by `Dapper`. The only difference is that these methods can be called from any object of type `IDbTransaction` instead of from an object of type `IDbConnection`.

- Execute
- ExecuteAsync
- ExecuteReader
- ExecuteReaderAsync
- ExecuteScalar
- ExecuteScalarAsync
- Query
- QueryAsync
- QueryFirst
- QueryFirstAsync
- QueryFirstOrDefault
- QueryFirstOrDefaultAsync
- QuerySingle
- QuerySingleAsync
- QuerySingleOrDefault
- QuerySingleOrDefaultAsync
- QueryMultiple
- QueryMultipleAsync

Let's consider the following example that shows how to call the extension method.

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
