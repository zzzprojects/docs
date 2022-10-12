---
Name: Async Action
---

# Async Action

Using Dapper Plus async method is an awesome way to improve your code's performance. By using asynchronous programming, you can avoid the blocking that can occur with traditional synchronous programming.

In addition, the dapper plus async method can help you improve the responsiveness of your UI by allowing your UI thread to continue processing while your database query is executing. As a result, your users will experience a much smoother and responsive UI. So if you're looking for a way to boost your code's performance.

Unfortunately, we don't provide a `BulkInsertAsync` method directly due to our chaining methods such as `AlsoBulkInsert` and `ThenBulkInsert`. Instead, you need to use the `BulkActionAsync` method that will execute your code asynchronously.

In this example, we call the `BulkInsert` method inside the `BulkActionAsync`:

```csharp
var task = connection.BulkActionAsync(x => x.BulkInsert(invoices));
```


## BulkActionAsync

To perfect any action asynchronous, you need to use the `BulkActionAsync` method.

In this example, we will insert asynchronous invoices and then all invoice items using a cancellation token.

```csharp
var task = connection.BulkActionAsync(x => x.BulkInsert(invoices).ThenBulkInsert(y => y.InvoiceItems), cancellationToken);
```

[Try it](https://dotnetfiddle.net/KR3qaX)