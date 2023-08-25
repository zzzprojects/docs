---
Name: Batch
---

# Batch

### BatchSize
Gets or sets the number of records to use in a batch.


```csharp
context.BulkMerge(options => options.BatchSize = 1000);
```

---

### BatchTimeout
Gets or sets the maximum of time in seconds to wait for a batch before the command throws a timeout exception.


```csharp
context.BulkMerge(options => options.BatchTimeout = 180);
```

---

### BatchDelayInterval
Gets or sets a delay in milliseconds to wait between batch.


```csharp
context.BulkMerge(list, options => options.BatchDelayInterval = 100);
```
