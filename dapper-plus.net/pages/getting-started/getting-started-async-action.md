# Async Action

## BulkActionAsync

To perform a bulk operation asynchronously, you need to use the BulkActionAsync method then in the expression, use non-asynchronous methods:

```csharp
connection.BulkActionAsync(x => x.BulkInsert(list));
```

## Roadmap Async

We are currently under development to offer all methods asynchronously:
- BulkInsertAsync
- BulkUpdateAsync
- BulkDeleteAsync
- BulkMergeAsync
- BulkSynchronizeAsync

Meanwhile, the only way is to use the BulkActionAsync method.
