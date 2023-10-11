---
id: 31c42167-fc84-41ef-931d-de21ff0dcdce
position: 2
title: How to use a stored procedure with INPUT parameters in Dapper
---

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

2. Use a [querying](https://www.learndapper.com/dapper-query) or [execute](https://www.learndapper.com/non-query) method with an anonymous types for your parameters

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