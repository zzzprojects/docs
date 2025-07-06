---
Name: Logging
---

# Logging

## Log
Gets or sets an action to `log` all database events as soon as they happen.


```csharp
StringBuilder logger = new StringBuilder();

context.BulkMerge(options =>
{
	options.Log += s => logger.AppendLine(s);
});
```

---

## UseLogDump
Gets or sets if all `log` events related to database events should be stored in a `LogDump` property.


```csharp
StringBuilder logDump;

context.BulkMerge(options =>
{
	options.UseLogDump = true;
	options.BulkOperationExecuted = bulkOperation => logDump = bulkOperation.LogDump;
});
```

---

## LogDump
Gets all `logged` database events when `UseLogDump` is enabled.


```csharp
StringBuilder logDump;

context.BulkMerge(options =>
{
	options.UseLogDump = true;
	options.BulkOperationExecuted = bulkOperation => logDump = bulkOperation.LogDump;
});
```
