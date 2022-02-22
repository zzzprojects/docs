---
PermaID: 1000193
Name: DeleteAll in Dapper Contrib
---

# Dapper Contrib - DeleteAll

## Description
DELETE ALL entities.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var isSuccess = connection.DeleteAll<Invoice>();
}
```