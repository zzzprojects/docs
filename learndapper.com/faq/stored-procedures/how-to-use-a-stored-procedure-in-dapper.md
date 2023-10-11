---
id: 06b88f1a-0c9d-44cb-a09c-d8812590e858
position: 1
title: How to use a stored procedure in Dapper?
---

To use a stored procedure, you need to follow these steps:

1. Create your stored procedure in your database:

```sql
CREATE PROCEDURE MyStoredProcedure
AS
BEGIN
    -- Your stored procedure logic here
END
```

2. Use a [querying](https://www.learndapper.com/dapper-query) or [execute](https://www.learndapper.com/non-query) method:

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