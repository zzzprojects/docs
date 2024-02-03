---
title: Dapper QueryUnbufferedAsync
description: The Dapper Query Unbuffered Async method is used to select data from your database asynchronously by loading objects on demand (unbuffered).
canonical: /dapper-query/selecting-unbuffered-async
status: Published
lastmod: 2024-02-03
---

# Querying Multiple Rows on Demand Asynchronously With Dapper

The method `QueryUnbufferedAsync` has been added in the [v2.0.138](https://github.com/DapperLib/Dapper/blob/8f369914ac4a03967fb68b3af6b0a85582473e3c/docs/index.md?plain=1#L68) to cover a very old mistake that requires breaking changes. The [QueryAsync](https://www.learndapper.com/dapper-query/selecting-multiple-rows#dapper-queryasync) extends the `IDbConnection` however the `IDbConnection` class doesn't have the `async` method as [NickCraver commented here](https://github.com/DapperLib/Dapper/issues/1921#issuecomment-1596325610).

The `Async` method should extend the `DbConnection` and not the `IDbConnection`, which will be done when the next major v3 version is released.

## Dapper Query Unbuffered Async

The method `QueryUnbufferedAsync` has 2 main purposes:

- Selecting multiple data from your database asynchronously
- Loading data retrieved on demand (similar to `buffered = false`)

In this example, we will start by using Dapper Plus to make the setup easier by using the [CreateTable](https://dapper-plus.net/create-table) method and [BulkInsert](https://dapper-plus.net/bulk-insert) method. Then, showing how to use the `QueryUnbufferedAsync` method from Dapper in a `foreach` statement using the `await` keyword and `ConfigureAwait` method:


```csharp
public static async Task Main()
{
	var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

	// CREATE table
	connection.CreateTable<Customer>();
	
	// SEED data
	var customers = new List<Customer>();
	customers.Add(new Customer() { Name = "Jonathan Magnan", Email = "info@zzzprojects.com" });
	customers.Add(new Customer() { Name = "ZZZ Projects", Email = "sales@zzzprojects.com" });
	customers.Add(new Customer() { Name = "Sara", Email = "sara@zzzprojects.com" });		
	connection.BulkInsert(customers);
	
	// SELECT data
	await foreach (var customer in connection.QueryUnbufferedAsync<Customer>("SELECT * FROM Customers WHERE CustomerID > @ID", new { ID  = 1 }).ConfigureAwait(false))
	{
		Console.WriteLine($"CustomerID: {customer.CustomerID}; Name: {customer.Name}; Email: {customer.Email}");
	}
}
```
[Online Example](https://dotnetfiddle.net/PB495V)

## Related Articles

- [Querying](/dapper-query)
- [Query & QueryAsync](/dapper-query/selecting-multiple-rows)
- [Stored Procedure](/stored-procedures)
- [Using Parameters](/parameters)
- [Buffered & Unbuffered Queries](/misc/buffered-unbuffered)