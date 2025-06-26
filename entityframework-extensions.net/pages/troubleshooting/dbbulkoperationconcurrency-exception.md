---
Name: DbBulkOperationConcurrency Troubleshooting
LastMod: 2025-06-26
---

# DbBulkOperationConcurrency

## Problem

You execute a method from the Entity Framework Extensions library, and the following error is thrown:

- Type: DbBulkOperationConcurrencyException

> A concurrency exception has occurred. Entities may have been modified or deleted since entities were loaded.

### Cause

Another thread already performed the operation.

### Solution

There are three possible resolutions:

- Database Win
- Client Win
- Custom Resolution

#### Database Win

```csharp
public void BulkUpdate_DatabaseWins<T>(CurrentContext ctx, List<T> list) where T : class
{
    try
    {
        ctx.BulkUpdate(list);
    }
    catch (DbBulkOperationConcurrencyException ex)
    {
        // DO nothing (or log), keep database values!
    }
}
```

#### Client Win

```csharp
public void BulkUpdate_StoreWins<T>(CurrentContext ctx, List<T> list) where T : class
{
    try
    {
        ctx.BulkUpdate(list);
    }
    catch (DbBulkOperationConcurrencyException ex)
    {
        // FORCE update store entities
        ctx.BulkUpdate(list, operation => operation.AllowConcurrency = false);
    }
}
```

#### Custom Resolution

```csharp
public void BulkUpdate_CustomResolution<T>(CurrentContext ctx, List<T> list) where T : class
{
    try
    {
        ctx.BulkUpdate(list);
    }
    catch (DbBulkOperationConcurrencyException ex)
    {
        foreach (var entry in ex.Entries)
        {
            ObjectStateEntry objectEntry;

            if (entry is EntitySimple_Concurrency)
            {
                var clientEntry = (EntitySimple_Concurrency) entry;
                var databaseEntry = ctx.EntitySimple_Concurrencies.Single(x => x.ID == clientEntry.ID);

                // merge properties like you want
                clientEntry.IntColumn = databaseEntry.IntColumn + 303;
            }
        }

        // FORCE update store entities
        ctx.BulkUpdate(list, operation => operation.AllowConcurrency = false);
    }
}
```
