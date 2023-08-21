---
id: b1c6a48b-38df-4d41-ae18-f6fd82b69bfd
position: 8
title: When using a stored procedure, do I need to add parameter names to the command text in Dapper?
---

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