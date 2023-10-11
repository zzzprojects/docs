---
id: 5be7a747-74a2-420e-ab4a-785fda58de3c
position: 1
title: How to retrieve a single row returned by a stored procedure in Dapper?
---

To retrieve a single row returned by a [stored procedure](https://www.learndapper.com/stored-procedures), you need to specify the stored procedure name in the command text and specify the command type to `CommandType.StoredProcedure`:

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
    var scalarValue = connection.QuerySingle<int>("MyStoredProcedure", commandType: CommandType.StoredProcedure);
    
    Console.WriteLine(scalarValue);
}

```