---
id: 2183c42b-c7a8-472f-abd5-bc91df88d3d1
position: 1
title: How to retrieve a specific column returned by a stored procedure in Dapper?
---

To return a specific column returned by a [stored procedure](https://www.learndapper.com/stored-procedures), you need to specify the stored procedure name in the command text and specify the command type to `CommandType.StoredProcedure`:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();

    var specificColumnList = result.Select(x => (string)x.MyColumn).ToList();
}
```