---
Name: Action Options
LastMod: 2023-03-01
---

# Action Options

## InsertIfNotExists

Insert entities only when no entities with the same key already exists in the destination.


```csharp
DapperPlusManager.Entity<Order>()
                 .InsertIfNotExists();

connection.BulkInsert(orders);

```
