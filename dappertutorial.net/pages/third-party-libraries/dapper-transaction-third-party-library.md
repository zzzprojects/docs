---
Title: Dapper Transaction - A Getting Started Tutorial to use Transaction
MetaDescription: Discover Dapper.Transaction Library to use query and execute methods from the `IDbTransaction` interface. Learn about Dapper.Transaction limitations and how to install it with NuGet.
LastMod: 2023-10-23
---

# Dapper Transaction: A Getting Started Tutorial to use Transaction

## What's Dapper Transaction?

**Dapper.Transaction** is exactly like `Dapper` but extend the `IDbTransaction` interface instead and use `Dapper` under the hood. It's a simple library to make it easier to work with a transaction.

 - **Dapper.Transaction** is a simple and lightweight transaction management tool for Dapper. 
 - Everything `Dapper` support, `Dapper Transaction` supports it as well (It's only a new extension method calling Dapper)
 - It was created to provide an easy way to manage transactions within the Dapper micro-ORM.

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

## NuGet Installation

**Dapper.Transaction** is only available through NuGet: <a href="https://www.nuget.org/packages/Dapper.Transaction/" target="_blank">https://www.nuget.org/packages/Dapper.Transaction/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper-Transaction 
```

## Method Supported

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