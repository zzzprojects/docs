---
Name: Transaction
LastMod: 2023-03-02
---

# Transaction

## Description

All Dapper Plus extension methods are also available on the IDbTransaction interface.


```csharp
transaction.BulkInsert(orders)
          .Include(x => x.ThenInsert(order => order.Items)
                         .ThenInsert(orderItem => orderItem.Metas))
          .Include(x => x.ThenInsert(order => order.Invoice)
                         .ThenInsert(Invoice => invoice.Items));
```

