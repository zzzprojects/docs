# Log

## Log Options
- [Log](#log)
- [UseLogDump](#uselogdump)
- [LogDump](#logdump)

## Log
Allow you to log some event happening in your database.

### Example
```csharp
StringBuilder logger = new StringBuilder();
            
// SAVE customers
using (var bulk = new BulkOperation(connection))
{
    // easy to use
    bulk.DestinationTableName = "Customers";
    bulk.Log += s => logger.AppendLine(s);
    bulk.BulkInsert(dtCustomers);
}
            
// SHOW data
Console.WriteLine(logger.ToString());
```
Try it: [.NET Core](https://dotnetfiddle.net/qyY7ca) | [.NET Framework](https://dotnetfiddle.net/3cTgCG)

## UseLogDump
Allow you to log in to a string (LogDump) event happening in your database.

### Example
```csharp
// SAVE customers
using (var bulk = new BulkOperation(connection))
{
    // easy to use
    bulk.DestinationTableName = "Customers";
    bulk.UseLogDump = true;
    bulk.BulkInsert(dtCustomers);
                
    var logDump = bulk.LogDump;
                
    // SHOW data
    Console.WriteLine(logDump.ToString());
}
```
Try it: [.NET Core](https://dotnetfiddle.net/SfsxSd) | [.NET Framework](https://dotnetfiddle.net/pGrUcP)

## LogDump
Allow you to retrieve the event happening in your database when UseLogDump is enabled.

### Example
```csharp
// SAVE customers
using (var bulk = new BulkOperation(connection))
{
    // easy to use
    bulk.DestinationTableName = "Customers";
    bulk.UseLogDump = true;
    bulk.BulkInsert(dtCustomers);
                
    var logDump = bulk.LogDump;
                
    // SHOW data
    Console.WriteLine(logDump.ToString());
}
```

Try it: [.NET Core](https://dotnetfiddle.net/YvFEgU) | [.NET Framework](https://dotnetfiddle.net/TvD8ZD)
