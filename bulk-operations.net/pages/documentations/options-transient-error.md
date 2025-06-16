---
Name: Transient Error
---

# Transient Error

## Transient Error Options
- [RetryCount](#retrycount)
- [RetryInterval](#retryinterval)

## RetryCount
Allows you to set how many times the bulk operation should retry the operation when a transient error occurs.

### Example

```csharp
var bulk = new BulkOperation(connection);

bulk.RetryCount = 3;

bulk.BulkMerge(dt);
```

## RetryInterval
Allows you to set how many times to wait before trying an operation again when a transient error occurs.

### Example

```csharp
var bulk = new BulkOperation(connection);

bulk.RetryCount = 3;
bulk.RetryInterval = new TimeSpan(100);

bulk.BulkMerge(dt);
```
