---
Name: Streaming in Entity Framework Extensions for EF Core & EF6
MetaDescription: Learn how to use streaming in Entity Framework Extensions for EF Core and EF6 to reduce memory usage and handle very large datasets efficiently.
LastMod: 2026-03-25
---

# Streaming in Entity Framework Extensions

Handling millions of rows in memory can break your application.

For example, if you read a large CSV file or generate millions of entities in a loop, loading everything into memory at once can quickly lead to an `OutOfMemory` exception.

Streaming in Entity Framework Extensions helps you solve this problem by sending records to the database in batches instead of all at once. This keeps memory usage low and allows you to process datasets that would not fit in memory otherwise.

## What streaming does

* Takes an `IEnumerable` and splits it into multiple smaller operations
* Materializes only one batch of entities at a time instead of the full collection
* Keeps memory usage stable, even when processing millions of rows

### Quick note about output values

When streaming, you cannot return output values from the database.

This means features like identity value propagation (for example, getting the generated `Id` after an insert) will not work.

If you insert rows that rely on identity columns, you should:

* Set `AutoOutputDirection = false`
* Or use [BulkInsertOptimized](/bulk-insert-optimized)

## EF Core: how to enable streaming

Set `UseStream = true`. The default `StreamSize` is `100,000`, but you can adjust it depending on your scenario.

Example:

```csharp
context.BulkInsert(entities, options =>
{
    options.UseStream = true;
    options.StreamSize = 100000; // Adjust based on memory and performance
    options.AutoOutputDirection = false; // Required if using identity columns
});
```

## EF6: how to enable streaming

EF6 does not use a `UseStream` flag. You only need to set `StreamSize`. This behavior is kept for backward compatibility.

Example:

```csharp
context.BulkInsert(entities, options =>
{
    options.StreamSize = 100000; // Adjust based on memory and performance
    options.AutoOutputDirection = false; // Required if using identity columns
});
```

## When should you use streaming?

You should consider streaming when:

* You are importing very large files (CSV, JSON, etc.)
* You are generating a large number of entities in memory (for example in a loop)
* You want to keep memory usage predictable during long operations
* You are working with millions of rows and want to avoid `OutOfMemory` exceptions

If your dataset is small, streaming is usually not necessary.

## Limitations

* Not compatible with [BulkSynchronize](/bulk-synchronize)
* Cannot return output values when streaming. If you need those, disable streaming or use another approach
  * No identity values
  * No computed column values
    
## Tips

* Start with the default `StreamSize` and adjust based on your memory usage and performance
* Larger batches can improve performance but increase memory usage
* Monitor your database transactions and connections, especially for long-running operations

## Summary

Streaming in EF Extensions splits large `IEnumerable` workloads into smaller batches, allowing you to process very large datasets without running out of memory.

In EF Core, enable it with `UseStream = true` and configure `StreamSize`. In EF6, simply set `StreamSize`.

Use it when working with millions of rows or very large files, but remember:
* You won’t get output values
* It is not compatible with `BulkSynchronize`

Test different batch sizes to find the best balance for your scenario.
