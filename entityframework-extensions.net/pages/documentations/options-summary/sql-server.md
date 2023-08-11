---
Name: SQL Server
LastMod: 2023-03-01
---

# SQL Server

## SqlBulkCopyOptions
Gets or sets the SqlBulkCopyOptions to use when `SqlBulkCopy` is used to directly insert in the destination table.


```csharp
context.BulkSaveChanges(options =>
{
   options.SqlBulkCopyOptions = SqlBulkCopyOptions.Default | SqlBulkCopyOptions.TableLock;
});
```

[Try it in EF Core](https://dotnetfiddle.net/ktEGo8) | [Try it in EF6](https://dotnetfiddle.net/j4jhLe)
