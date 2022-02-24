---
Name: DestinationTableName
---

# SqlBulkCopy - DestinationTableName

## Description
Name of the destination table on the server.

- Type: System.Int32
- Default Value: String.Empty

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    // SET DestinationTableName value.
    sqlBulk.DestinationTableName = "Customers";
    
    // ...ColumnMappings & Options...
    sqlBulk.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/WQKpAE)

## Remarks

 - If `DestinationTableName` has not been set when WriteToServer is called, an `ArgumentNullException` is thrown.
 - If `DestinationTableName` is modified while a WriteToServer operation is running, the change does not affect the current operation. 
 - The new `DestinationTableName` value is used the next time a WriteToServer method is called.