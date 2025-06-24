---
Name: TemporaryTableUseTableLock
LastMod: 2025-06-24
---

# TemporaryTableUseTableLock

## Description

Gets or sets whether the temporary table must be locked when inserting records into it.


```csharp
context.BulkSaveChanges(options =>
{
   options.TemporaryTableUseTableLock = true;
});
```

## Purpose
Using table lock increases the overall performance when inserting into a temporary table. This option should not be disabled.
