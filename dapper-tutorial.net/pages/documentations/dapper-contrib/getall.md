---
PermaID: 1000195
Name: GetAll in Dapper Contrib
---

# Dapper Contrib - GetAll

## Description
GET ALL entities.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.GetAll<Invoice>().ToList();
}
```