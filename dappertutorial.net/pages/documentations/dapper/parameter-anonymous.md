---
PermaID: 1000169
Title: Dapper Anonymous Type Parameter - Learn How to Avoid SQL Injection
MetaDescription: Unlock the power of Dapper with Anonymous Type Parameter to avoid SQL Injection. Learn how to easily pass your parameter without worrying about user input.
LastMod: 2023-10-20
---

# Dapper Anonymous Type Parameter: Discover How to Avoid SQL Injection

## Description

Dapper make it simple & safe (SQL Injection) to use parameter by supporting anonymous type. 

 - Anonymous parameters are used in cases where you don't want to hardcode the parameter names in your query. 
 - This can be useful if you want to dynamically build a query, or if you want to avoid SQL injection attacks. 

The raw SQL query can be executed using the [Execute](/execute) method with anonymous parameters. These are useful for simple queries where you don't need to create a separate class to represent your data. This can save you a considerable amount of time and effort when working with complex SQL queries.

### Single

When you need to insert a single record, you can pass the `INSERT` statement directly to the `Execute` method. It also allows you to use parameterized `INSERT` statement as shown in the below example.

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var affectedRows = connection.Execute(sql, new {CustomerName = "Mark"});

	Console.WriteLine(affectedRows);
	
	// Only for see the Insert.
	var customer = connection.Query<Customer>("SELECT * FROM CUSTOMERS WHERE CustomerName = 'Mark'").ToList();

	FiddleHelper.WriteTable(customer);
}
```

In the example above, we use the [Execute](/execute) to insert a customer named "Mark". We use an anonymous parameter for the `CustomerName` value. We also use the [Query](/query) method to return all customer with the name `Mark`.

Try it: [.NET Core](https://dotnetfiddle.net/wNl0G3) | [.NET Framework](https://dotnetfiddle.net/Z1iRIQ)

### Many

If you need to insert multiple records into the database then you can use the `Execute` method with a SQL statement that uses a parameterized insert statement. This will execute the `INSERT` statement for each record in the parameters object. 


```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{			
	var affectedRows = connection.Execute(sql,
	new[]
	{
	new {CustomerName = "John"},
	new {CustomerName = "Andy"},
	new {CustomerName = "Allan"}
	}
	
	Console.WriteLine(affectedRows);
)
```

Try it: [.NET Core](https://dotnetfiddle.net/df2ZDH) | [.NET Framework](https://dotnetfiddle.net/fvRKsY)

To insert, multiple record, you can also use the [Dapper Plus - BulkInsert](https://dappertutorial.net/bulk-insert) methods

You can also use anonymous parameters with the [Query](/query) and [QueryFirst](/queryfirst) methods. 

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetail = connection.QueryFirst<OrderDetail>(sql, new {OrderDetailID = 10});
	
	FiddleHelper.WriteTable(new List<OrderDetail>() { orderDetail } );
}
```

In this example, the variable `orderDetail` would be of type `dynamic`. This means that you can access the columns of the result set by their names.

Try it: [.NET Core](https://dotnetfiddle.net/5GFAdJ) | [.NET Framework](https://dotnetfiddle.net/1K2DU4)

## Related Articles

- [Dapper Execute](/execute)
- [Dapper Plus - BulkInsert](https://dappertutorial.net/bulk-insert)
- [Dapper Query](/query)
- [Dapper QueryFirst](/queryfirst)