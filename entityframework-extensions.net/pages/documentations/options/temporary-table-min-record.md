---
Name: TemporaryTableMinRecord
LastMod: 2025-06-24
---

# TemporaryTableMinRecord

## Description

Gets or sets the minimum number of records to use a temporary table instead of using SQL derived table.


```csharp
context.BulkSaveChanges(options =>
{
   options.TemporaryTableMinRecord = 25;
});
```

## Purpose
Our library is smart but finding the `optimal` number is very hard since there are a lot of factors. Increasing the default value may improve the performance.
