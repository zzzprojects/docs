---
Name: RetryInterval
LastMod: 2025-06-24
---

# RetryInterval

## Description

Gets or sets the interval to wait before retrying an operation when a transient error occurs.


```csharp
context.BulkSaveChanges(options => {
	options.RetryCount = 3;
	options.RetryInterval = new TimeSpan(100);
});

```

## Purpose
A transient error is a temporary error that is likely to disappear soon. That rarely happens but they might occur!

This option helps reduce a bulk operation failure by retrying it when a transient error occurs.

## FAQ

### What are transient errors?
You can find a list of transient errors here: [Transient fault error codes](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-develop-error-messages#transient-fault-error-codes)

Which includes the most common errors such as:
- Cannot open database
- The service is currently busy
- Database is not currently available
- Not enough resources to process request
