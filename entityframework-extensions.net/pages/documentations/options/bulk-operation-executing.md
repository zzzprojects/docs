---
Name: BulkOperationExecuting
LastMod: 2025-06-23
---

# BulkOperationExecuting

## Description
Gets or sets an action to execute before the bulk operation is executed.


```csharp
context.BulkSaveChanges(options => {
	options.BulkOperationExecuting = bulkOperation => { /* configuration */ };
});
```

## Purpose
This event allows you to check or change some options that have been automatically added when none are specified such as Batch Size, Column Mapping, Timeout, etc.
