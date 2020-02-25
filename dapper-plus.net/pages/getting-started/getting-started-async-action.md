# Async Action

## BulkActionAsync

To perform a bulk operation asynchronously, you need to use the BulkActionAsync method then in the expression, use non-asynchronous methods:

```csharp
var task = connection.BulkActionAsync(x => x.BulkInsert(invoices));
```

[Try it](https://dotnetfiddle.net/KR3qaX)

## Roadmap Async

We are currently under development to offer all methods asynchronously:
- BulkInsertAsync
- BulkUpdateAsync
- BulkDeleteAsync
- BulkMergeAsync
- BulkSynchronizeAsync

Meanwhile, the only way is to use the BulkActionAsync method.
