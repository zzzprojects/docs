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

> We only added non-asynchronous version in this tutorial to make it easier to read.

## ExecuteAsync

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var affectedRows = connection.ExecuteAsync(sql, new {CustomerName = "Mark"}).Result;

	Console.WriteLine(affectedRows);

	var customer = connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerName = 'Mark'").ToList();

	FiddleHelper.WriteTable(customer);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/2rVSi0' %}

## QueryAsync
```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetails = connection.QueryAsync<OrderDetail>(sql).Result.ToList();

	Console.WriteLine(orderDetails.Count());

	FiddleHelper.WriteTable(orderDetails);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/X79bZI' %}

## QueryFirstAsync
```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetail = connection.QueryFirstAsync<OrderDetail>(sql, new {OrderDetailID = 1}).Result;

	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/7Jbdcg' %}

## QueryFirstOrDefaultAsync
```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetail = connection.QueryFirstOrDefaultAsync<OrderDetail>(sql, new {OrderDetailID = 1}).Result;

	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/26NWaz' %}

## QuerySingleAsync
```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{			
	var orderDetail = connection.QuerySingleOrDefaultAsync<OrderDetail>(sql, new {OrderDetailID = 1}).Result;

	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/pmjYFp' %}

## QuerySingleOrDefaultAsync
```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetail = connection.QuerySingleOrDefaultAsync<OrderDetail>(sql, new {OrderDetailID = 1}).Result;

	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/WvbA02' %}

## QueryMultipleAsync
```csharp
var sql = "SELECT * FROM Invoice; SELECT * FROM InvoiceItem;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	using (var multi = connection.QueryMultipleAsync(sql, new { InvoiceID = 1 }).Result)
	{
		var invoice = multi.Read<Invoice>().First();
		var invoiceItems = multi.Read<InvoiceItem>().ToList();
	}
}
```
