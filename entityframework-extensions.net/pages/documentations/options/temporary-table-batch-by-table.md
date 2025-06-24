---
Name: TemporaryTableBatchByTable
LastMod: 2025-06-24
---

# TemporaryTableBatchByTable

## Description

Gets or sets the number of batches that a temporary table can contain. This option may create multiple temporary tables when the number of batches to execute exceeds the limit specified.


```csharp
context.BulkSaveChanges(options =>
{
   options.TemporaryTableBatchByTable = 0; // unlimited
});
```

## Purpose
So far, we have not found any scenario that could require it. But we still support this option!
