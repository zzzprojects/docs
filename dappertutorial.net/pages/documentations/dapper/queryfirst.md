---
PermaID: 1000174
Title: Dapper Query First - Learn How to Return the First Result Row
MetaDescription: Unlock the power of Dapper Query First to optimize your C# database operations. Learn how to use QueryFirst and QueryFirstAsync to return an anonymous type or strongly typed entity.
LastMod: 2023-10-20
---

# Dapper Query First: Discover How to Return the First Result Row

## Description

The `QueryFirst` extension method in Dapper is one of the simplest and most useful methods. 

 - It executes a SQL query and returns the first result as an object. 
 - It is incredibly useful when you need to just get a single record from the database, for example when validating a user's credentials. 

The `QueryFirst` method can be called from any object of type `IDbConnection` and the result can be mapped to:

- [Anonymous](#example---query-anonymous)
- [Strongly Typed](#example---query-strongly-typed)

### Parameters

The syntax of the `QueryFirst<T>` method is given below:

```csharp
T QueryFirst<T>(string sql, 
                object param = null, 
				IDbTransaction transaction = null, 
				int? commandTimeout = null, 
				CommandType? commandType = null);
```

The `QueryFirst` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.

The `QueryFirst` method also takes optional parameters for transaction, command timeout, and command type.

The following table shows the different parameters of a `QueryFirst` method.

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

## Example - QueryFirst Anonymous

The raw SQL query can be executed using the `QueryFirst` method and map the first result to a dynamic list. 

You could store the results of this query in a [Dapper anonymous result type](/result-anonymous) as follows:

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QueryFirst(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```

These are useful for simple queries where you don't need to create a separate class to represent your data. This can save you a considerable amount of time and effort when working with complex SQL queries.

Try it: [.NET Core](https://dotnetfiddle.net/CDQKMN) | [.NET Framework](https://dotnetfiddle.net/eogWc1)

## Example - Query Strongly Typed

Dapper provides you with the ability to get your [query results as strongly typed objects](/result-strongly-typed) instead of just getting back a dynamic object. The raw SQL query can be executed using the `QueryFirst` method and map the first result to a strongly typed list.

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
    var orderDetail = connection.QueryFirst<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```

In the above example, you can see that Dapper makes it very easy to map your query results to strongly typed objects. 

Try it: [.NET Core](https://dotnetfiddle.net/FTB4ih) | [.NET Framework](https://dotnetfiddle.net/AV0OgZ)

## Related Articles

- [Dapper - Anonymous Result](/result-anonymous) 
- [Dapper - Strongly Typed Result](/result-strongly-typed)
