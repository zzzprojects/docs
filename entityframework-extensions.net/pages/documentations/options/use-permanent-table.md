---
Name: UsePermanentTable
LastMod: 2025-06-24
---

# UsePermanentTable

## Description

Gets or sets if the library should `create` and `drop` a permanent table instead of using a temporary table.


```csharp
context.BulkSaveChanges(options =>
{
   options.UsePermanentTable = true;
});
```

## Purpose
This option can be useful when, for some rare reason, you don't have access to the `tempdb` database.
