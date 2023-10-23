---
PermaID: 1000178
Title: Dapper Query SingleOrDefault - Learn How to Return a Single Row 
MetaDescription: Unlock the power of Dapper Query SingleOrDefault to optimize your C# database operations. Learn how to use QuerySingleOrDefault and QuerySingleOrDefault to return an anonymous type or strongly typed entity.
LastMod: 2023-10-20
---

# Dapper Query SingleOrDefault: Discover How to Return a Single Row or Default Value

## Description

Dapper's `QuerySingleOrDefault` method is used to execute a query and return the first row of the results, or the default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence. 

 - This method is useful for retrieving a single row from a database table when you know there will only be one matching row.
 - If there are multiple matching rows in the results set, the `QuerySingleOrDefault` method will throw an exception.

The `QuerySingleOrDefault` method can be called from any object of type `IDbConnection` and the result can be mapped to:

- [Anonymous](#example---query-anonymous)
- [Strongly Typed](#example---query-strongly-typed)

### Parameters

The syntax of the `QuerySingleOrDefault<T>` method is given below:

```csharp
T QuerySingleOrDefault<T>(string sql, 
                          object param = null, 
                          IDbTransaction transaction = null, 
                          int? commandTimeout = null, 
                          CommandType? commandType = null);
```

The `QuerySingleOrDefault` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.

The `QuerySingleOrDefault` method also takes optional parameters for transaction, command timeout, and command type.

The following table shows the different parameters of a `QuerySingleOrDefault` method.

| Name | Description |
| :--- | :---------- |
| sql            | It represents an SQL query or stored procedure. This parameter is required. |
| param          | It represents the parameters required by SQL query or stored procedure. This parameter is optional. We can pass the parameter to SQL in anonymous type, `dynamic` object, or `Dapper.DynamicParameters` class. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |
| commandType    | It specifies how SQL query or stored procedure should be interpreted by the data provider. The default value of this parameter is Text. This parameter is optional. |

### First, Single & Default
First, Single & Default methods are very different. When choosing between the different methods of querying data from a database using Dapper, it is important to understand the differences between them to make the best decision for your particular needs.

`QueryFirst` and `QuerySingle` both return a single record from the database, be careful to use the right method because there are some important distinctions between the two that are mentioned in the following table.

| Result          | No Item   | One Item | Many Items |
| :-------------- | :-------: | :------: | :--------: |
| First           | Exception | Item     | First Item |
| Single          | Exception | Item     | Exception  |
| FirstOrDefault  | Default   | Item     | First Item |
| SingleOrDefault | Default   | Item     | Exception  |

When deciding which method to use, it is important to consider the particular needs of your application. 


## Example - QuerySingleOrDefault Anonymous

The raw SQL query can be executed using the `QuerySingleOrDefault` method and map the first result to a dynamic list, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

For example, let's say we have a database table of `OrderDetails` and we want to retrieve the `OrderDetail` with the `OrderDetailID` of 1. You could store the results of this query in a [Dapper anonymous result type](/result-anonymous) as follows:


```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QuerySingleOrDefault(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```

These are useful for simple queries where you don't need to create a separate class to represent your data. This can save you a considerable amount of time and effort when working with complex SQL queries.

Try it: [.NET Core](https://dotnetfiddle.net/uG7LPt) | [.NET Framework](https://dotnetfiddle.net/nYmbCo)

## Example - QuerySingleOrDefault Strongly Typed

Dapper provides you with the ability to get your query results as strongly typed objects instead of just getting back a dynamic object. 

The raw SQL query can be executed using the `QuerySingleOrDefault` method and map the first result to a [strongly typed list](/result-strongly-typed), or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.

First, let's create a simple class that we can use to map our query results and we will call this class `OrderDetail`.

```csharp
public class OrderDetail
{
	public int OrderDetailID { get; set; }
	public int OrderID { get; set; }
	public int ProductID { get; set; }
	public int Quantity { get; set; }
}
```

Next, we write our SQL query to fetch a particular OrderDetail from our database.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetail = connection.QuerySingleOrDefault<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail });
}
```

In the above example, you can see that Dapper makes it very easy to map your query results to strongly typed objects. 

Try it: [.NET Core](https://dotnetfiddle.net/BNTmxc) | [.NET Framework](https://dotnetfiddle.net/kFMKnL)

## Related Articles

- [Dapper - Anonymous Result](/result-anonymous) 
- [Dapper - Strongly Typed Result](/result-strongly-typed)
