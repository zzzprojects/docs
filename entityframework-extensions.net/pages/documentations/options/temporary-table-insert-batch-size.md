---
Name: TemporaryTableInsertBatchSize
LastMod: 2023-03-01
---

# TemporaryTableInsertBatchSize

## Description

Gets or sets the number of records to use in a batch when inserting in a temporary table. This number is recommended to be high.


```csharp
context.BulkSaveChanges(options =>
{
   options.TemporaryTableInsertBatchSize = 50000;
});
```

## Purpose
Increasing the default value may improve the performance. Since the temporary table doesn't contain index and trigger and it's normally locked during the insert, you may use a very high value.
