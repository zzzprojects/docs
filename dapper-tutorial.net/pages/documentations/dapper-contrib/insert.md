---
PermaID: 1000196
Name: Insert in Dapper Contrib
---

# Dapper Contrib - Insert

## Description
INSERT a single or many entities.

- [Insert Single](#example---insert-single)
- [Insert Many](#example---insert-single)

## Example - Insert Single
INSERT a single entitiy.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var identity = connection.Insert(new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1"});
}
```

## Example - Insert Many
INSERT many entities.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var list = new List<InvoiceContrib>
    {
        new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Many_1"},
        new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Many_2"},
        new InvoiceContrib {Kind = InvoiceKind.StoreInvoice, Code = "Insert_Many_3"}
    };

    var identity = connection.Insert(list);
}
```