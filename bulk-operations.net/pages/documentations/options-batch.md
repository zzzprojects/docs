# Batch

## Batch Options
- [BatchSize](#batchsize)
- [BatchTimeout](#batchtimeout)
- [BatchDelayInteval](#batchdelayinterval)

## BatchSize
Allow you to set the number of records to use in a batch.

For example, if you insert 1000 entities, and you set a batch size of 100, then ten inserts will be performed.

### Example
```csharp
using (var bulk = new BulkOperation(connection))
{
    // easy to use
    bulk.DestinationTableName = "Customers";
    bulk.BatchSize = 100;
    bulk.BulkInsert(dtCustomers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/Bi7fxY) | [.NET Framework](https://dotnetfiddle.net/L4nq5q)

## BatchTimeout
Allow you to set the maximum of time elapsed for a batch before the command throws a timeout exception.

### Example
```csharp
using (var bulk = new BulkOperation(connection))
{
    // easy to use
    bulk.DestinationTableName = "Customers";
    bulk.BatchTimeout = 180;
    bulk.BulkInsert(dtCustomers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/E7OGci) | [.NET Framework](https://dotnetfiddle.net/qJAZ5t)

## BatchDelayInterval
Allow you to set a delay between every batch.

### Example
```csharp
using (var bulk = new BulkOperation(connection))
{
    // easy to use
    bulk.DestinationTableName = "Customers";
    bulk.BatchDelayInterval = 100;
    bulk.BulkInsert(dtCustomers);
}
```
> WARNING: Be careful, this options can often cause lock/deadlock within a transaction.

Try it: [.NET Core](https://dotnetfiddle.net/ieWLt8) | [.NET Framework](https://dotnetfiddle.net/9ZErjY)
