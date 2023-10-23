---
PermaID: 1000170
Title: Dapper Dynamic Parameter - Learn How to Add Parameter on Demand
MetaDescription: Unlock the power of Dapper with Dynamic Parameter to add value on demand. Learn how to pass any amount of parameters at runtime.
LastMod: 2023-10-20
---

# Dapper Dynamic Parameter: Discover How to Add Parameter on Demand

## Description

One of the features that make Dapper stand out is its support for dynamic parameters. Dynamic parameters are useful when you want to pass a variable number of arguments to a SQL statement or

 - This can be useful if you want to dynamically build a query, or if you want to avoid SQL injection attacks.
 - Dynamic parameters are useful when you don't know at compile time what kind of parameters will be used in a query. For example, you might want to construct a query dynamically based on user input. Dapper makes it easy to work with dynamic parameters.


To use Dynamic parameters with Dapper, you need to use the `DynamicParameters` class. The `DynamicParameters` class has a dynamic object that represents the anonymous parameters. 

Once you have created your parameter object, you can pass it to the Dapper methods that execute queries. Dapper will automatically extract the parameters from the object and add them to the query.

Here is an example of how to use anonymous parameters with Dapper: 

### Single

You can use dynamic parameters with Dapper's [Execute method](/execut)). Here's an example of how to use dynamic parameters with Dapper's `Execute` method in which we will [insert](/execute#example-execute-insert) a single record. 

```csharp
var sql = "EXEC Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	DynamicParameters parameter = new DynamicParameters();

	parameter.Add("@Kind", InvoiceKind.WebInvoice, DbType.Int32, ParameterDirection.Input);
	parameter.Add("@Code", "Many_Insert_0", DbType.String, ParameterDirection.Input);
	parameter.Add("@RowCount", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

	connection.Execute(sql,
		parameter,
		commandType: CommandType.StoredProcedure);

	int rowCount = parameter.Get<int>("@RowCount");
}
```

In the example above, we created a `DynamicParameters` object and add three dynamic parameters to it, and then we used the `Execute` method to execute the SQL query with the dynamic parameters. 

### Many

If you need to insert multiple records into the database then you can use the `Execute` method and pass the list of `DynamicParameters` objects as shown in the below example. 

```csharp
var sql = "EXEC Invoice_Insert";

var parameters = new List<DynamicParameters>();

for (var i = 0; i < 3; i++)
{
	var p = new DynamicParameters();
	p.Add("@Kind", InvoiceKind.WebInvoice, DbType.Int32, ParameterDirection.Input);
	p.Add("@Code", "Many_Insert_" + (i + 1), DbType.String, ParameterDirection.Input);
	p.Add("@RowCount", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

	parameters.Add(p);
}

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	connection.Execute(sql,
		parameters,
		commandType: CommandType.StoredProcedure
	);

	var rowCount = parameters.Sum(x => x.Get<int>("@RowCount"));
}
```

As you can see, using dynamic parameters with Dapper is very simple. It can make your code more flexible and easier to work with. Give it a try and see how it can help you in your projects.

To insert, multiple record, you can also use the [Dapper Plus - BulkInsert](https://dappertutorial.net/bulk-insert) methods

## Related Articles

- [Dapper Execute](/execute)
- [Dapper Plus - BulkInsert](https://dappertutorial.net/bulk-insert)