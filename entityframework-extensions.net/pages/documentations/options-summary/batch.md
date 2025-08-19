---
Title: Batch Options within Entity Framework Extensions  
MetaDescription: Learn how to use BatchSize, BatchTimeout, and BatchDelayInterval in Entity Framework Extensions to optimize performance during bulk operations. Control batch size, timeout, and delay with flexible configuration options.  
LastMod: 2025-08-19
---

# 📦 Batch Options in Entity Framework Extensions /n Control how data is sent to the database

When working with our [bulk extensions](/bulk-extensions), performance can often be improved by [configuring](/configure-options) a few important options.

Here, you'll learn about three key options available in Entity Framework Extensions:

- [BatchSize](#batchsize): Number of records per batch. Helps balance speed and resource usage.
- [BatchTimeout](#batchtimeout): Maximum time (in seconds) before a batch operation times out.
- [BatchDelayInterval](#batchdelayinterval): Delay (in milliseconds) between each batch. Not recommended in a transaction.

These settings help you control how many records are processed at once, how long a batch can run before timing out, and whether there's a delay between batches.

---

## 🏷️ BatchSize

The `BatchSize` option in Entity Framework Extensions defines how many rows are included in each batch. Once the batch is full, it gets sent to the database server.

- A batch is considered "complete" once it reaches the configured size or there are no more rows to send.
- Default value: `0` (auto-selected based on the provider and operation type)
   - SQL Server: `10000`
   - PostgreSQL: `10000`
   - MySQL: `200`
   - MariaDB: `200`
   - SQLite: `1`
   - Firebird: `125`
   - Oracle: `2000` for insert/delete, `200` for update/merge
   - InMemory: `int.MaxValue`
   - [Effort](https://entityframework-effort.net/): `int.MaxValue`

Here’s how to modify the batch size when performing a [BulkInsert](/bulk-insert):

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Insert in chunks of 100 rows
context.BulkInsert(options => options.BatchSize = 100);
````

[Online Example (EF Core)](https://dotnetfiddle.net/qonEbL) | [Online Example (EF6)](https://dotnetfiddle.net/BThvHs)

---

### 💡 What is the recommended BatchSize?

There’s no one-size-fits-all answer—you need to test based on your environment.

- Too low? You'll make many round-trips to the server.
- Too high? You'll send fewer requests, but each one could take longer and consume more memory.

What impacts performance:

- Column size
- Indexes
- Server latency
- Triggers
- Network speed
- And more!

You might find that in your development environment, `BatchSize = 25000` is optimal, while in production, `BatchSize = 2000` works better due to other constraints.

💡 Our library is already fast! Don’t over-optimize unless there’s a real need. If everything runs fast enough, there’s usually no reason to change it.

---

## 🏷️ BatchTimeout

The `BatchTimeout` option in Entity Framework Extensions sets how long (in seconds) the library should wait for a batch to complete before timing out.

- Default value: The `context.Database.GetCommandTimeout()` value, if one is specified.

A batch timeout error might look like this:

```csharp
System.Exception: 'A timeout error occurred (Current Timeout = 30). Please increase the timeout globally: ctx.Database.SetCommandTimeout(timeoutValue); or BulkOperationManager.BulkOperationBuilder = operation => operation.BatchTimeout = timeoutValue; or by operation: db.BulkSaveChanges(operation => operation.BatchTimeout = timeoutValue);'
```

To fix this, you can increase the timeout using Entity Framework:

```csharp
context.Database.SetCommandTimeout(300);
```

Or through our library:

- [Set a specific timeout option](/configure-options#configuring-a-global-option)
- [Configure a global timeout option](/configure-options#configuring-a-global-option)

Here’s how to set a timeout for a specific [BulkInsert](/bulk-insert):

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(options => options.BatchTimeout = 180);
```

[Online Example (EF Core)](https://dotnetfiddle.net/Uvvffx) | [Online Example (EF6)](https://dotnetfiddle.net/HDmeWa)

---

## 🏷️ BatchDelayInterval

The `BatchDelayInterval` option in Entity Framework Extensions sets a pause (in milliseconds) between each batch operation.

- Default value: `0` (no delay)

Here’s how to set the delay for a [BulkInsert](/bulk-insert):

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(list, options => options.BatchDelayInterval = 100);
```

[Online Example (EF Core)](https://dotnetfiddle.net/lil9tq) | [Online Example (EF6)](https://dotnetfiddle.net/65v3k3)

⚠️ **Important:** Avoid using this setting inside a transaction. A transaction should be short and fast. Adding a delay:

- Increases the time the transaction stays open
- Raises the risk of locks and deadlocks
- Can negatively affect concurrency and reliability

---

## 🔎 Comparison at a Glance

| Option                 | What it controls              | Default Value              | Best For                         |
| ---------------------- | ----------------------------- | -------------------------- | -------------------------------- |
| **BatchSize**          | Rows sent per round-trip      | Auto-selected per provider | Balancing speed & memory         |
| **BatchTimeout**       | Max seconds before timeout    | EF context timeout         | Handling very large operations   |
| **BatchDelayInterval** | Delay between each batch (ms) | 0 (no delay)               | Reducing DB stress in rare cases |

---

## 📌 When to Use

Batch options are useful when you:

* Insert/update **millions of rows**.
* Hit **timeouts** with large batches.
* Need to **throttle load** on the database.

Most of the time, defaults are already tuned for performance — only tweak these if you hit real issues.

---

## ⭐ Why It’s Useful

With batch options you can:

* Make operations faster by adjusting `BatchSize`.
* Prevent crashes with `BatchTimeout`.
* Fine-tune load handling (rarely) with `BatchDelayInterval`.

---

## 📚 Related Articles

- [Configuring Options](/configure-options)
- [Bulk Extensions](/bulk-extensions)
- [Bulk Insert](/bulk-insert)

---

## 🏁 Conclusion

Tweaking `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` can help you squeeze more performance out of your bulk operations—but only when really needed.

- Use `BatchSize` to control how many rows are sent in one go.
- Use `BatchTimeout` if you encounter timeout exceptions for large operations.
- Use `BatchDelayInterval` only in very specific scenarios—**never** inside a transaction.

Most of the time, the default settings are already optimized for typical use cases. Unless you're facing performance issues or working with millions of records, you probably don’t need to change them.

🎯 The goal isn’t to "tweak everything"—it's to fix problems when they show up using the right option.