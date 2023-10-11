---
id: 474b4282-255c-46de-bc67-16a272267745
position: 5
title: How to use a stored procedure to retrieve a returned scalar value in Dapper?
---

To use a stored procedure and retrieve a returned scalar value, you need to use the [ExecuteScalar](https://www.learndapper.com/dapper-query/selecting-scalar-values) method:

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