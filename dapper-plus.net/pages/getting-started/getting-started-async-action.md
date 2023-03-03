---
Name: Getting Started with Async Action
LastMod: 2023-02-26
---

# Async Action

## BulkActionAsync

To perform a bulk operation asynchronously, you need to use the BulkActionAsync method then in the expression, use non-asynchronous methods:

```csharp
var task = connection.BulkActionAsync(x => x.BulkInsert(invoices).ThenBulkInsert(y => y.InvoiceItems), cancellationToken);
```

[Try it](https://dotnetfiddle.net/KR3qaX)