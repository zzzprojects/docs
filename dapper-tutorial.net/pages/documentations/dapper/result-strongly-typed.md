---
PermaID: 1000183
Name: Result Strongly Typed
---

# Dapper - Result Strongly Typed 

## Description
Extension methods can be used to execute a query and map the result using strongly typed.

The anonymous result can be mapped from following methods:

- [Query](#example---query)
- [QueryFirst](#example---queryfirst)
- [QueryFirstOrDefault](#example---queryfirstordefault)
- [QuerySingle](#example---querysingle)
- [QuerySingleOrDefault](#example---querysingleordefault)

These extension methods can be called from any object of type IDbConnection.

## Example - Query
Query method can execute a query and map the result to a strongly typed list.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetails = connection.Query<OrderDetail>(sql).ToList();

    Console.WriteLine(orderDetails.Count);

    FiddleHelper.WriteTable(orderDetails);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/CvMkj8) | [.NET Framework](https://dotnetfiddle.net/dXZc0s)

## Example - QueryFirst
QueryFirst method can execute a query and map the first result to a strongly typed list.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetail = connection.QueryFirst<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable( new List<OrderDetail>() { orderDetail });
}
```
Try it: [.NET Core](https://dotnetfiddle.net/FTB4ih) | [.NET Framework](https://dotnetfiddle.net/AV0OgZ)

## Example - QueryFirstOrDefault
QueryFirstOrDefault method can execute a query and map the first result to a strongly typed list, or a default value if the sequence contains no elements.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QueryFirstOrDefault<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```

Try it: [.NET Core](https://dotnetfiddle.net/Ct5P3q) | [.NET Framework](https://dotnetfiddle.net/2WQ7sc)

## Example - QuerySingle
QuerySingle method can execute a query and map the first result to a strongly typed list and throws an exception if there is not exactly one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetail = connection.QuerySingle<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```
Try it: [.NET Core](https://dotnetfiddle.net/oEsh6D) | [.NET Framework](https://dotnetfiddle.net/vnkv7q)

## Example - QuerySingleOrDefault
QuerySingleOrDefault method can execute a query and map the first result to a strongly typed list, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetail = connection.QuerySingleOrDefault<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```
Try it: [.NET Core](https://dotnetfiddle.net/BNTmxc) | [.NET Framework](https://dotnetfiddle.net/kFMKnL)
