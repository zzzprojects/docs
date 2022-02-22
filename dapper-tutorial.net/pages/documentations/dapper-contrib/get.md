---
PermaID: 1000194
Name: Get in Dapper Contrib
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