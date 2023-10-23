---
PermaID: 1000165
Title: Dapper Async Methods - Learn How Execute Query Asynchronously
MetaDescription: Unlock the power of Dapper by using Async Methods. Learn how to use methods such as 'ExecuteAsync' and 'QueryAsync' in your async methods.
LastMod: 2023-10-20
---

# Dapper Async Methods: Discover How Execute Query Asynchronously

## Description

Dapper has several methods that allow you to execute asynchronous queries. To use the async functionality, you need to pass in a `System.Data.IDbConnection` and call one of the extension methods provided by Dapper.

Dapper extends the `IDbConnection` interface with the following Async (asynchronous) methods.

- [ExecuteAsync](#executeasync): It executes a query asynchronously and returns the number of rows affected.
- [QueryAsync](#queryasync): It executes a query asynchronously and returns the results of the query as an `IEnumerable<T>`.
- [QueryFirstAsync](#queryfirstasync): It executes a query asynchronously and returns the first result of the query.
- [QueryFirstOrDefaultAsync](#queryfirstordefaultasync): It executes a query asynchronously and returns the first result of the query, or the default value for the type T if no result is found.
- [QuerySingleAsync](#querysingleasync): It executes a query asynchronously and returns a single result of the query.
- [QuerySingleOrDefaultAsync](#querysingleordefaultasync): It executes a query asynchronously and returns a single result of the query, or the default value for the type T if no result is found.
- [QueryMultipleAsync](#querymultipleasync): It executes multiple queries asynchronously within the same command and maps the results to strong entities.

> We only added a non-asynchronous version in this tutorial to make it easier to read.

## ExecuteAsync

The `ExecuteAsync` method can execute a query one or multiple times asynchronously and return the number of affected rows. The following example shows how to use the `ExecuteAsync` method by inserting a customer 

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var affectedRows = await connection.ExecuteAsync(sql, new {CustomerName = "Mark"}).ConfigureAwait(false);

	Console.WriteLine(affectedRows);
	
	var customers = await connection.QueryAsync<Customer>("Select * FROM CUSTOMERS WHERE CustomerName = 'Mark'").ConfigureAwait(false);
	
	FiddleHelper.WriteTable(customers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/xnZ8IU)

## QueryAsync

The `QueryAsync` can execute a query and map the result asynchronously.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{			
	var orderDetails = await connection.QueryAsync<OrderDetail>(sql).ConfigureAwait(false);

	Console.WriteLine(orderDetails.Count());
	
	FiddleHelper.WriteTable(orderDetails);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/hHb3wO)

## QueryFirstAsync

The `QueryFirstAsync` can execute a query and map asynchronously the first result.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetail = await connection.QueryFirstAsync<OrderDetail>(sql, new {OrderDetailID = 1}).ConfigureAwait(false);
	
	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
Try it: [.NET Core](https://dotnetfiddle.net/uZlDVp)

## QueryFirstOrDefaultAsync

The `QueryFirstOrDefaultAsync` can execute a query and map asynchronously the first result, or a default value if the sequence contains no elements.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{			
	var orderDetail = await connection.QueryFirstOrDefaultAsync<OrderDetail>(sql, new {OrderDetailID = 1}).ConfigureAwait(false);
	
	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
Try it: [.NET Core](https://dotnetfiddle.net/dc8hgc)

## QuerySingleAsync

The `QuerySingleAsync` can execute a query and map asynchronously the first result and throws an exception if there is not exactly one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.Open();
	
	var orderDetail = await connection.QuerySingleAsync<OrderDetail>(sql, new {OrderDetailID = 1}).ConfigureAwait(false);

	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
Try it: [.NET Core](https://dotnetfiddle.net/q0xyFF)

## QuerySingleOrDefaultAsync

The `QuerySingleOrDefaultAsync` can execute a query and map asynchronously the first result, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetail = await connection.QuerySingleOrDefaultAsync<OrderDetail>(sql, new {OrderDetailID = 1}).ConfigureAwait(false);
	
	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
Try it: [.NET Core](https://dotnetfiddle.net/ZPq3LL)

## QueryMultipleAsync

The `QueryMultipleAsync` can execute multiple queries within the same command and map results asynchronously.

```csharp
var sql = "SELECT TOP 3 * FROM Orders; SELECT TOP 3 * FROM OrderDetails;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.Open();

	using (var multi = await connection.QueryMultipleAsync(sql).ConfigureAwait(false))
	{
		var orders = multi.Read<Order>().ToList();
		var orderDetails = multi.Read<OrderDetail>().ToList();
		
		FiddleHelper.WriteTable(orders);
		FiddleHelper.WriteTable(orderDetails);
	}
}
```

Try it: [.NET Core](https://dotnetfiddle.net/RiQFaO)
