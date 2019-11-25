---
PermaID: 1000179
Name: Result Anonymous
---

# Dapper - Result Anonymous 

## Description
Extension methods can be used to execute a query and map the result using dynamic.

The anonymous result can be mapped from following extension methods:

- [Query](#example---query)
- [QueryFirst](#example---queryfirst)
- [QueryFirstOrDefault](#example---queryfirstordefault)
- [QuerySingle](#example---querysingle)
- [QuerySingleOrDefault](#example---querysingleordefault)

These extension methods can be called from any object of type IDbConnection.
## Example - Query
Query method can execute a query and map the result to a dynamic list.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetails = connection.QueryFirstOrDefault(sql);

    FiddleHelper.WriteTable(orderDetails);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/0L9hnQ) | [.NET Framework](https://dotnetfiddle.net/y925xR)

## Example - QueryFirst
QueryFirst method can execute a query and map the first result to a dynamic list.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QueryFirst(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/CDQKMN) | [.NET Framework](https://dotnetfiddle.net/eogWc1)

## Example - QueryFirstOrDefault
QueryFirstOrDefault method can execute a query and map the first result to a dynamic list, or a default value if the sequence contains no elements.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetail = connection.QueryFirstOrDefault(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/OdxIah) | [.NET Framework](https://dotnetfiddle.net/58YMxR)

## Example - QuerySingle
QuerySingle method can execute a query and map the first result to a dynamic list and throws an exception if there is not exactly one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    var orderDetail = connection.QuerySingle(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/ZYSItm) | [.NET Framework](https://dotnetfiddle.net/uEq0HC)

## Example - QuerySingleOrDefault
QuerySingleOrDefault method can execute a query and map the first result to a dynamic list, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QuerySingleOrDefault(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/uG7LPt) | [.NET Framework](https://dotnetfiddle.net/nYmbCo)
