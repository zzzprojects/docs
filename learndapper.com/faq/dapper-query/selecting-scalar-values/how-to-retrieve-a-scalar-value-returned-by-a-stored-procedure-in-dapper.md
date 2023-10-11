---
id: 6e2ecdcc-c485-4bfd-9f05-d0e1e40ef96e
position: 1
title: How to retrieve a scalar value returned by a stored procedure in Dapper?
---

To retrieve a scalar value returned by a [stored procedure](https://www.learndapper.com/stored-procedures), you need to specify the stored procedure name in the command text and specify the command type to `CommandType.StoredProcedure`:

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