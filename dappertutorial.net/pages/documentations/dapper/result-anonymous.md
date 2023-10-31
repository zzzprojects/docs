---
PermaID: 1000179
Title: Dapper Anonymous Result - Learn How to Map Result to Dynamic Object
MetaDescription: Unlock the power of Dapper by returning an Anonymous Result to map to a dynamic object. Learn how to query your database and return a non-strongly typed result.
LastMod: 2023-10-20
---

# Dapper Anonymous Result: Discover How to Map Result to Dynamic Object

## Description

Dapper's anonymous result is one of the key features that sets it apart from other ORMs. It allows you to map your query results to an anonymous list of objects, without having to create a separate type. These are useful for simple queries where you don't need to create a separate class to represent your data. This can save you a considerable amount of time and effort when working with complex SQL queries. 

 - When we query a database, we often want to map the results to a strongly typed object. However, sometimes we just want to get a list of key-value pairs or an anonymous type.
 - This can be extremely useful when working with dynamic data, or when your data structure is constantly changing.

The anonymous result can be mapped from the following extension methods:

- [Query](#example-query)
- [QueryFirst](#example-queryfirst)
- [QueryFirstOrDefault](#example-queryfirstordefault)
- [QuerySingle](#example-querysingle)
- [QuerySingleOrDefault](#example-querysingleordefault)

These extension methods can be called from any object of type `IDbConnection`.

## Example - Query

The raw SQL query can be executed using the [Query](/query) method and map the result to a dynamic list. 

The following example shows how to map the `Query` method results to an anonymous result type.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetails = connection.Query(sql);

    FiddleHelper.WriteTable(orderDetails);
}
```

In this example, the variable `orderDetails` would be of type `dynamic`. This means that you can access the columns of the result set by their names.

Try it: [.NET Core](https://dotnetfiddle.net/0L9hnQ) | [.NET Framework](https://dotnetfiddle.net/y925xR)

## Example - QueryFirst

The [QueryFirst](/queryfirst) method can execute a query and map the first result to a dynamic list.

The following example shows how to map the `QueryFirst` method results to an anonymous result type.

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

The [QueryFirstOrDefault](/queryfirstordefault) method can execute a query and map the first result to a dynamic list, or a default value if the sequence contains no elements.

The following example shows how to map the `QueryFirstOrDefault` method results to an anonymous result type.

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

The [QuerySingle](/querysingle) method can execute a query and map the first result to a dynamic list and throws an exception if there is not exactly one element in the sequence.

The following example shows how to map the `QuerySingle` method results to an anonymous result type.

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

The [QuerySingleOrDefault](/querysingleordefault) method can execute a query and map the first result to a dynamic list, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

The following example shows how to map the `QuerySingleOrDefault` method results to an anonymous result type.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QuerySingleOrDefault(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/uG7LPt) | [.NET Framework](https://dotnetfiddle.net/nYmbCo)

## Related Articles

- [Dapper - Query](/query)
- [Dapper - QueryFirst](/queryfirst)
- [Dapper - QueryFirstOrDefault](/queryfirstordefault)
- [Dapper - QuerySingle](/querysingle)
- [Dapper - QuerySingleOrDefault](/querysingleordefault)