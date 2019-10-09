---
PermaID: 1000192
Name: Delete
---

# Dapper Contrib - Delete

## Description
DELETE a single or many entities.

- [Delete Single](#example---delete-single)
- [Delete Many](#example---delete-single)

## Example - Delete Single
DELETE a single entitiy.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var isSuccess = connection.Delete(new Invoice {InvoiceID = 1});
}
```

## Example - Delete Many
DELETE many entities.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var list = new List<Invoice>()
    {
        new Invoice {InvoiceID = 1},
        new Invoice {InvoiceID = 2},
        new Invoice {InvoiceID = 3}
    };

    var isSuccess = connection.Delete(list);
}
```