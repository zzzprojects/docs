---
Name: Transient Error
---

# Transient Error

## RetryCount
Gets or sets the maximum number of operations to retry when a transient error occurs.


```csharp
context.BulkMerge(options => {
	options.RetryCount = 3;
});
```

---

## RetryInterval
Gets or sets the interval to wait before retrying an operation when a transient error occurs.


```csharp
context.BulkMerge(options => {
	options.RetryCount = 3;
	options.RetryInterval = new TimeSpan(100);
});

```
