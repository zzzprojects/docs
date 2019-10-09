---
PermaID: 1000194
Name: Get
---

# Dapper Contrib - Get

## Description
Get a single entity by ID.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.Get<Invoice>(1);
}
```