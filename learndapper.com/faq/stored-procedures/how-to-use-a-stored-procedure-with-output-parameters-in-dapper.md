---
id: de092c67-ecf2-49a9-b05a-70f6ea0ce62a
position: 3
title: How to use a stored procedure with OUTPUT parameters in Dapper?
---

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
3. Use a [querying](https://www.learndapper.com/dapper-query) or [execute](https://www.learndapper.com/non-query) method with your parameters
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