---
id: 0b588cb3-d96a-477a-8b5b-402cf7358fad
position: 7
title: When using a stored procedure, do I need to add 'EXEC' to the command text in Dapper?
---

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