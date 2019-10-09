---
PermaID: 1000197
Name: Update
---

# Dapper Contrib - Update

## Description
UPDATE a single or many entities.

- [Update Single](#example---update-single)
- [Update Many](#example---update-single)

## Example - Update Single
UPDATE a single entitiy.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var isSuccess = connection.Update(new Invoice { InvoiceID = 1, Code = "Update_Single_1"});
}
```

## Example - Update Many
UPDATE many entities.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var list = new List<Invoice>()
    {
        new Invoice {InvoiceID = 1, Code = "Update_Many_1"},
        new Invoice {InvoiceID = 2, Code = "Update_Many_2"},
        new Invoice {InvoiceID = 3, Code = "Update_Many_3"}
    };

    var isSuccess = connection.Update(list);
}
```