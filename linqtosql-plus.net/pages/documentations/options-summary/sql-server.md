# SQL Server

## SqlBulkCopyOptions
Gets or sets the SqlBulkCopyOptions to use when `SqlBulkCopy` is used to directly insert in the destination table.


```csharp
context.BulkSubmitChanges(options =>
{
   options.SqlBulkCopyOptions = SqlBulkCopyOptions.Default | SqlBulkCopyOptions.TableLock;
});
```
