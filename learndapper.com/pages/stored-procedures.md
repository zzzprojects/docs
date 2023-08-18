---
title: Dapper Stored Procedure
description: All Dapper querying methods allow executing stored procedures without writing extra code by specifying the commandType parameter to 'StoredProcedure'.
canonical: /stored-procedures
status: Published
lastmod: 2023-08-01
---

# Executing Stored Procedures With Dapper

Stored procedures are a great way to encapsulate business logic and reduce the complexity of handling multiple queries. It can also improve performance by optimizing query execution. 

 - Stored procedures also provide better security since they are isolated from the application code, making it harder to manipulate data outside of the stored procedure.
 - Stored procedures are also easier to maintain since they are centrally located and can be modified from one place. 
 - It helps reduce the chances of duplicate queries and makes it easier to identify errors quickly.

To demonstrate this feature, let's create a simple Customer table:

```csharp
CREATE TABLE [Customer]
(
    [CustomerID] [INT] IDENTITY(1,1) NOT NULL,
    [FirstName] [VARCHAR](100) NULL,
    [LastName] [VARCHAR](100) NULL,
    [BirthDate] [DATE] NULL
)
```

We will execute a stored procedure that takes an id parameter and returns the Customer with that particular id. Here is the stored procedure code:

```csharp
CREATE PROCEDURE GetCustomerById 
    (@id int)  
AS  
BEGIN    
    SELECT * FROM Customer WHERE CustomerID = @id  
END 
```

Now that we have the stored procedure created let's look at how to execute it using Dapper.

To begin with, we need to create a connection.

Next, we need to set up the parameters that will be used to pass values into the stored procedure. We can do this using a `DynamicParameters` object and adding each parameter's values. In this case, our parameter is an `id` of type `int`. 

Finally, we can execute the stored procedure using extend methods from Dapper, such as the `Execute` method and pass in our parameters. In our case, we used the [QuerySingleOrDefault](/dapper-query/selecting-single-rows) method to map the result to a `Customer` object containing all the data from the retrieved record. 
Here is an example of how this code might look:

```csharp
using(var connection = new SqlConnection(connectionString))
{
    //Set up DynamicParameters object to pass parameters  
    DynamicParameters parameters = new DynamicParameters();   
    parameters.Add("id", 1);  
	
    //Execute stored procedure and map the returned result to a Customer object  
    var customer = conn.QuerySingleOrDefault<Customer>("GetCustomerById", parameters, commandType: CommandType.StoredProcedure);
}
```

You have now successfully executed a stored procedure using Dapper. That example can be expanded to include more complex stored procedures, which may consist of multiple parameters and return a list of objects. 

There are two ways to execute stored procedures with Dapper: with the `CommandType` as `Text`; or as `StoredProcedure`. 

## Dapper CommandType.Text

All `DbCommand` objects have a `CommandType` property. By default, this is set to `Text`. If you want to execute a stored procedure in a SQL statement (text), you use the [Execute (or Exec) statement](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql?view=sql-server-2017):

```csharp
var sql = "EXEC GetSalesByYear @BeginningDate, @EndingDate";
var values = new { BeginningDate = "2017-01-01", EndingDate = "2017-12-31" };
var results = connection.Query(sql, values).ToList();
results.ForEach(r => Console.WriteLine($"{r.OrderID} {r.Subtotal}"));
```

The name of the stored procedure, in this case, is **GetSalesByYear**. Parameters are supplied as a comma-separated list after the name of the procedure.

## Dapper CommandType.StoredProcedure

Dapper provides access to the `CommandType` property via the `commandType` parameter included in all the various [querying methods](/dapper-query) provided by Dapper. 

The next example is the equivalent to the above, but with the `CommandType` set to `StoredProcedure`:

```csharp
var storedProcedureName = "GetSalesByYear";
var values = new { BeginningDate = "2017-01-01", EndingDate = "2017-12-31" };
var results = connection.Query(storedProcedureName, values, commandType: CommandType.StoredProcedure).ToList();
results.ForEach(r => Console.WriteLine($"{r.OrderID} {r.Subtotal}"));
```

It's recommand to always set the command type to `CommandType.StoredProcedure` when using a stored procedure in Dapper for multiple reasons:

- **Code Clarity**: Using `CommandType.StoredProcedure` communicates the intent of your code more clearly.
- **Security**: By specifying `CommandType.StoredProcedure`, you are less susceptible to SQL injection attacks because the stored procedure name and parameters are specified separately.
- **Performance**: SQL Server can optimize the execution of stored procedures better than arbitrary SQL statements.
- **Parameter Handling**: Dapper can handle output and return parameters correctly when `CommandType.StoredProcedure` is used. If you use 'EXEC' with `CommandType.Text`, you would need to manually handle output and return parameters.


## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-use-a-stored-procedure-in-dapper" itemprop="name">How to use a stored procedure in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To use a stored procedure, you need to follow these steps:

1. Create your stored procedure in your database:

```sql
CREATE PROCEDURE MyStoredProcedure
AS
BEGIN
    -- Your stored procedure logic here
END
```

2. Use a [querying](/dapper-query) or [execute](/non-query) method:

```csharp
using(var connection = new SqlConnection(connectionString))
{
	// Execute the stored procedure
	var result = connection.Query<Customer>(
		"MyStoredProcedure",
		commandType: CommandType.StoredProcedure
	).ToList();
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-use-a-stored-procedure-with-input-parameters-in-dapper" itemprop="name">How to use a stored procedure with INPUT parameters in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To use a stored procedure with input parameters, you need to follow these steps:

1. Create your stored procedure in your database with input parameters:

```sql
CREATE PROCEDURE MyStoredProcedure
    @InputParam1 INT,
    @InputParam2 INT
AS
BEGIN
    -- Your stored procedure logic here
END
```

2. Use a [querying](/dapper-query) or [execute](/non-query) method with an anonymous types for your parameters

```csharp
using(var connection = new SqlConnection(connectionString))
{
	// Define parameters including your output parameters
	var parameters = new DynamicParameters();
	parameters.Add("@InputParam1", inputParam1Value);
	parameters.Add("@InputParam2", inputParam2Value);
	
	// Execute the stored procedure
	var result = connection.Query<Customer>(
		"MyStoredProcedure",
		new { InputParam1 = inputParam1Value, InputParam2 = inputParam2Value},
		commandType: CommandType.StoredProcedure
	).ToList();
}
```

3. Or create a `DynamicParameters` object and add parameter to it.


```csharp
using(var connection = new SqlConnection(connectionString))
{
	// Define parameters including your output parameters
	var parameters = new DynamicParameters();
	parameters.Add("@InputParam1", inputParam1Value);
	parameters.Add("@InputParam2", inputParam2Value);
	
	// Execute the stored procedure
	var result = connection.Query<Customer>(
		"MyStoredProcedure",
		parameters,
		commandType: CommandType.StoredProcedure
	).ToList();
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-use-a-stored-procedure-with-output-parameters-in-dapper" itemprop="name">How to use a stored procedure with OUTPUT parameters in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To use a stored procedure with an output parameter, you need to follow these steps:

1. Create your stored procedure in your database with an output parameter:

```sql
CREATE PROCEDURE MyStoredProcedure
    @InputParam1 INT,
    @OutputParam2 INT OUTPUT
AS
BEGIN
    -- Your stored procedure logic here
END
```


2. Create a `DynamicParameters` object and add parameter to it. For an output parameter, you must specify `ParameterDirection.Output`
3. Use a [querying](/dapper-query) or [execute](/non-query) method with your parameters
4. Retrieve the parameter value from the parameters passed to the stored procedure:

```csharp
using(var connection = new SqlConnection(connectionString))
{
	// Define parameters including your output parameters
	var parameters = new DynamicParameters();
	parameters.Add("@InputParam1", inputParam1Value);
	parameters.Add("@OutputParam2", dbType: DbType.Int32, direction: ParameterDirection.Output);
	
	// Execute the stored procedure
	var result = connection.Execute(
		"MyStoredProcedure",
		parameters,
		commandType: CommandType.StoredProcedure
	);
	
	// Get the output parameter value
	var outputParam2Value = parameters.Get<int>("@OutputParam2");
	
	// Do something with the output parameter value..
	Console.WriteLine($"Output parameter value: {outputParam2Value}");
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-use-a-stored-procedure-to-retrieve-a-returned-specific-column-in-dapper" itemprop="name">How to use a stored procedure to retrieve a returned specific column in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To use a stored procedure and retrieve a returned specific column, you have 3 choices:

1. By mapping returned data to an anonymous type and select your column:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();

    var specificColumnList = result.Select(x => (string)x.MyColumn).ToList();
}
```

2. By mapping returned data to a strongly typed object and select your column:

```csharp
public class MyStoredProcedureResult
{
    public string MyColumn { get; set; }
	
    // Add other properties as needed, to match all columns returned by the stored procedure
}
	
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query<MyStoredProcedureResult>("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();

    var specificColumnList = result.Select(x => x.MyColumn).ToList();
}
```

3. By using [QueryMultiple (Multi-Mapping)](/dapper-query/selecting-multiple-results) and read returned data with an anonymous type or strongly typed object:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    using (var multi = connection.QueryMultiple("MyStoredProcedure", commandType: CommandType.StoredProcedure))
    {
        var firstResultSet = multi.Read().ToList();
        var specificColumnFromFirstResultSet = firstResultSet.Select(x => (string)x.MyColumn).ToList();
    }
}
```

:::{.alert .alert-warning}
NOTE: All columns are returned from the stored procedure, not only the desired selected column.
:::

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-use-a-stored-procedure-to-retrieve-a-returned-scalar-value-in-dapper" itemprop="name">How to use a stored procedure to retrieve a returned scalar value in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To use a stored procedure and retrieve a returned scalar value, you need to use the [ExecuteScalar](/dapper-query/selecting-scalar-values) method:

```sql
CREATE PROCEDURE MyStoredProcedure
AS
BEGIN
    SELECT 1
END
```

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var scalarValue = connection.ExecuteScalar<int>("MyStoredProcedure", commandType: CommandType.StoredProcedure);
    
    Console.WriteLine(scalarValue);
}

```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-use-a-stored-procedure-to-retrieve-multiple-returned-results-in-dapper" itemprop="name">How to use a stored procedure to retrieve multiple returned results in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To use a stored procedure and retrieve multiple returned results, you need to use the [QueryMultiple](/dapper-query/selecting-multiple-results) method and read every results by either mapping it to an anoymous type or a strongly typed object:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    connection.Open();
    using (var results = connection.QueryMultiple("MyStoredProcedure", commandType: CommandType.StoredProcedure))
    {
        // Anonymous Type
        var resultSet1 = results.Read().ToList();
		
        // Strongly Typed Object
        var resultSet2 = results.Read<ResultType2>().ToList();
    }
}
	
public class ResultType2
{
	public int ID { get; set; }
	public string Name { get; set; }
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="when-using-a-stored-procedure-do-i-need-to-add-exec-to-the-command-text-in-dapper" itemprop="name">When using a stored procedure, do I need to add 'EXEC' to the command text in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

You don't need to add `EXEC` to the command text when specifing the command type to `CommandType.StoredProcedure`. However, you need add `EXEC` if you don't specify a  command type or you set the command type to `CommandType.Text`.

1. With `CommandType.StoredProcedure`, you don't have to use EXEC:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();
}
```

2. With `CommandType.Text`, you must use EXEC:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("EXEC MyStoredProcedure", commandType: CommandType.Text).ToList();
}
```

3. With no command type specified, you must use EXEC:

```
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("EXEC MyStoredProcedure").ToList();
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="when-using-a-stored-procedure-do-i-need-to-add-parameter-names-to-the-command-text-in-dapper" itemprop="name">When using a stored procedure, do I need to add parameter names to the command text in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

You don't need to add parameter names to the command text when specifing the command type to `CommandType.StoredProcedure`. However, you need add `EXEC` and parameter names if you don't specify a  command type or you set the command type to `CommandType.Text`.

1. With `CommandType.StoredProcedure`, you don't have to use EXEC:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var parameters = new DynamicParameters();
    parameters.Add("@Parameter1", value1);
    parameters.Add("@Parameter2", value2);
	
    var result = connection.Query("MyStoredProcedure", parameters, commandType: CommandType.StoredProcedure).ToList();
}
```

2. With `CommandType.Text`, you must use EXEC and specify parameter names:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var parameters = new DynamicParameters();
    parameters.Add("@Parameter1", value1);
    parameters.Add("@Parameter2", value2);

    var result = connection.Query("EXEC MyStoredProcedure @Parameter1, @Parameter2", parameters, commandType: CommandType.Text).ToList();
}

```

</div></div>
</details>

</div>

## Related Articles

- [Querying](/dapper-query)
- [Querying Scalar Values](/dapper-query/selecting-scalar-values)
- [Querying Multiple Results](/dapper-query/selecting-multiple-results)
- [Executing Non-Query](/non-query)
- [Using Parameters](/parameters)

