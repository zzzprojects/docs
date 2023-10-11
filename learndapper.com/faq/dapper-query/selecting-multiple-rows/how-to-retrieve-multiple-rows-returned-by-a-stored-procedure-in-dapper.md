---
id: 8483286b-c4ac-42bc-a494-bac596ae2632
position: 1
title: How to retrieve multiple rows returned by a stored procedure in Dapper?
---

To retrieve multiple rows returned by a [stored procedure](https://www.learndapper.com/stored-procedures), you need to specify the stored procedure name in the command text and specify the command type to `CommandType.StoredProcedure`.

1. First, create your stored procedure:

```sql
CREATE PROCEDURE MyStoredProcedure
AS
BEGIN
    SELECT * FROM Customer
END
```

2. Map returned data to an anonymous type or a strongly typed object:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();
}
```

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string Name { get; set; }
	
    // Add other properties as needed, to match all columns returned by the stored procedure
}
	
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query<Customer>("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();
}
```