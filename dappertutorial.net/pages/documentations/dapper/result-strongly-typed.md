---
PermaID: 1000183
Title: Dapper Strongly Typed Result - Learn How to Map Result to Entity
MetaDescription: Unlock the power of Dapper by returning a Strongly Typed Result to map your entity type or DTO. Learn how to query your database and automatically map the result to your entity model and domain classes.
LastMod: 2023-10-20
---

# Dapper Strongly Typed Result: Discover How to Map Result to Entity

## Description

Dapper provides you with the ability to run strongly typed queries against your database. It means that you can get your query results as strongly typed objects instead of just getting back a dynamic object. In this article, we will take a look at how to use Dapper's methods to map our query results to a strongly typed list of objects.

The strongly typed result can be mapped from the following methods:

- [Query](#example---query)
- [QueryFirst](#example---queryfirst)
- [QueryFirstOrDefault](#example---queryfirstordefault)
- [QuerySingle](#example---querysingle)
- [QuerySingleOrDefault](#example---querysingleordefault)

These extension methods can be called from any object of type `IDbConnection`. First, let's create a simple class that we can use to map our query results and we will call this class `OrderDetail`.

```csharp
public class OrderDetail
{
	public int OrderDetailID { get; set; }
	public int OrderID { get; set; }
	public int ProductID { get; set; }
	public int Quantity { get; set; }
}
```

Next, we write our SQL query to fetch a particular `OrderDetail` from our database.

## Example - Query

The raw SQL query can be executed using the [Query](/query) method and map the result to a strongly typed list.

The following example shows how to map the `Query` method results to a strongly typed result type.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetails = connection.Query<OrderDetail>(sql).ToList();

    Console.WriteLine(orderDetails.Count);

    FiddleHelper.WriteTable(orderDetails);
}
```

In the above example, the variable `orderDetails` would be a list of strongly typed objects. 

Try it: [.NET Core](https://dotnetfiddle.net/CvMkj8) | [.NET Framework](https://dotnetfiddle.net/dXZc0s)

## Example - QueryFirst

The [QueryFirst](/queryfirst) method can execute a query and map the first result to a strongly typed list.

The following example shows how to map the `QueryFirst` method results to a strongly typed result type.

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

The [QueryFirstOrDefault](/queryfirstordefault) method can execute a query and map the first result to a strongly typed list, or a default value if the sequence contains no elements.

The following example shows how to map the `QueryFirstOrDefault` method results to a strongly typed result type.

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

The [QuerySingle](/querysingle) method can execute a query and map the first result to a strongly typed list and throws an exception if there is not exactly one element in the sequence.

The following example shows how to map the `QuerySingle` method results to a strongly typed result type.

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

The [QuerySingleOrDefault](/querysingleordefault) method can execute a query and map the first result to a strongly typed list, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

The following example shows how to map the `QuerySingleOrDefault` method results to a strongly typed result type.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetail = connection.QuerySingleOrDefault<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```
Try it: [.NET Core](https://dotnetfiddle.net/BNTmxc) | [.NET Framework](https://dotnetfiddle.net/kFMKnL)

## Related Articles

- [Dapper - Query](/query)
- [Dapper - QueryFirst](/queryfirst)
- [Dapper - QueryFirstOrDefault](/queryfirstordefault)
- [Dapper - QuerySingle](/querysingle)
- [Dapper - QuerySingleOrDefault](/querysingleordefault)