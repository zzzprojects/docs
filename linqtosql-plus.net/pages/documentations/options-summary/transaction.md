---
Name: Transaction
---

# Transaction

## Bulk Operations
Bulk Operations such as BulkInsert, BulkUpdate, BulkDelete doesn't use a transaction by default. This is your responsibility to handle it.

If you start a transaction within LinqToSql, Bulk Operations will honor it.


```csharp
var transaction = context.Connection.BeginTransaction();
try
{
	context.BulkInsert(list1);
	context.BulkInsert(list2);
	transaction.Commit();
}
catch
{
	transaction.Rollback();
}
```
