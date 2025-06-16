---
Name: Execute Event
---

# Execute Event

## Execute Event Options
- [BulkOperationExecuting](#bulkoperationexecuting)
- [BulkOperationExecuted](#bulkoperationexecuted)

## BulkOperationExecuting
Allows you to change configuration before the bulk operation is executed.

### Example
```csharp
var bulk = new BulkOperation(connection);

bulk.BulkOperationExecuting = bulkOperation => { /* configuration */ };

bulk.BulkMerge(dt);
```

## BulkOperationExecuted
Allows you to change configuration after the bulk operation is executed.

### Example
```csharp
var bulk = new BulkOperation(connection);

bulk.BulkOperationExecuted = bulkOperation => { /* configuration */ };

bulk.BulkMerge(dt);
```
