---
PermaID: 1000165
Name: Async
---

# Dapper - Async

## Description
Dapper also extend the IDbConnection interface with Async (asynchronous) methods:
- [ExecuteAsync](#executeasync)
- [QueryAsync](#queryasync)
- [QueryFirstAsync](#queryfirstasync)
- [QueryFirstOrDefaultAsync](#queryfirstordefaultasync)
- [QuerySingleAsync](#querysingleasync)
- [QuerySingleOrDefaultAsync](#querysingleordefaultasync)
- [QueryMultipleAsync](#querymultipleasync)

> We only added a non-asynchronous version in this tutorial to make it easier to read.

## ExecuteAsync

The `ExecuteAsync` can execute a command one or multiple times asynchronously and return the number of affected rows. 

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
