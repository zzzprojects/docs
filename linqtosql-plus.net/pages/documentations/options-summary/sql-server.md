---
Name: SQL Server
---

# SQL Server

## SqlBulkCopyOptions
Gets or sets the SqlBulkCopyOptions to use when `SqlBulkCopy` is used to directly insert in the destination table.


```csharp
context.BulkMerge(options =>
{
   options.SqlBulkCopyOptions = (int)(SqlBulkCopyOptions.Default | SqlBulkCopyOptions.TableLock);
});
```
