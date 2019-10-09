# Transaction

## BulkSubmitChanges
As SubmitChanges, BulkSubmitChanges already save all entities within an internal transaction. So by default, there is nothing to do.

However, if you start a transaction within LinqToSql, BulkSaveChanges will honor it and will use this transaction instead of creating an internal transaction.


```csharp
var transaction = context.Connection.BeginTransaction();
try
{
	context.BulkSubmitChanges();
	transaction.Commit();
}
catch
{
	transaction.Rollback();
}
```
---

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
