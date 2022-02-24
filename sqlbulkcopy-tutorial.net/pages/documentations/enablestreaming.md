---
Name: EnableStreaming
---

# SqlBulkCopy - EnableStreaming

## Description
Enables/disables a `SqlBulkCopy` object to stream data from an `IDataReader` object

- Type: System.Boolean
- Default Value: false

If a SqlBulkCopy object can stream data from an IDataReader object, the value is `true`; otherwise, `false`.
## Example

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    // SET EnableStreaming value.
    sqlBulk.EnableStreaming = true;
    
    sqlBulk.DestinationTableName = "Customers";
    sqlBulk.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/YePJYT)

## Remarks

 - When `EnableStreaming` is `true`, SqlBulkCopy reads from an IDataReader object using SequentialAccess, optimizing memory usage by using the IDataReader streaming capabilities. 
 - When it's set to `false`, the SqlBulkCopy class loads all the data returned by the IDataReader object into memory before sending it to SQL Server or SQL Azure.