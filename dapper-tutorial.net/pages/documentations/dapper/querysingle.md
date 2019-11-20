---
PermaID: 1000177
Name: QuerySingle
---

# Dapper - QuerySingle 

## Description
QuerySingle method is an extension method that can be called from any object of type IDbConnection. It can execute a query and map the first result and throws an exception if there is not exactly one element in the sequence.

The result can be mapped to:

- [Anonymous](#example---query-anonymous)
- [Strongly Typed](#example---query-strongly-typed)

### Parameters
The following table shows the different parameters of a QuerySingle method.

| Name | Description |
| :--- | :---------- |
| sql            | The query to execute. |
| param          | The query parameters (default = null). |
| transaction    | The transaction to use (default = null). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

### First, Single & Default
Be careful to use the right method. First & Single methods are very different.

| Result          | No Item   | One Item | Many Items |
| :-------------- | :-------: | :------: | :--------: |
| First           | Exception | Item     | First Item |
| Single          | Exception | Item     | Exception  |
| FirstOrDefault  | Default   | Item     | First Item |
| SingleOrDefault | Default   | Item     | Exception  |

## Example - Query Anonymous
Execute a query and map the first result to a dynamic list, and throws an exception if there is not exactly one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{	
	var orderDetail = connection.QuerySingle(sql, new {OrderDetailID = 1});

	FiddleHelper.WriteTable(orderDetail);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/ZYSItm) | [.NET Framework](https://dotnetfiddle.net/uEq0HC)

## Example - Query Strongly Typed
Execute a query and map the first result to a strongly typed list, and throws an exception if there is not exactly one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{			
	var orderDetail = connection.QuerySingle<OrderDetail>(sql, new {OrderDetailID = 1});

	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```
Try it: [.NET Core](https://dotnetfiddle.net/oEsh6D) | [.NET Framework](https://dotnetfiddle.net/vnkv7q)
