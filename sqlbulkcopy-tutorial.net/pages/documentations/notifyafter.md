# SqlBulkCopy - NotifyAfter

## Description
Defines the number of rows to be processed before generating a notification event.

- Type: System.Int32
- Default Value: 0 (None)
- If NotifyAfter is set to a number less than zero, an `ArgumentOutOfRangeException` is thrown.

The `NotifyAfter` property is set so that the event handler is called after every 200 rows copied to the table.

```csharp
using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connectionString))
{
    // SET NotifyAfter value.
    bulkCopy.NotifyAfter = 200;

    bulkCopy.SqlRowsCopied += (sender, args) => { /* code */ };

    bulkCopy.DestinationTableName = "TheDestinationTable";
    bulkCopy.WriteToServer(reader);
}
```

[Try it](https://dotnetfiddle.net/IhQ00C)

## Remarks

 - The `NotifyAfter` property is designed for user interface components that illustrate the progress of a bulk copy operation. 
 - It indicates the number of rows to be processed before generating a notification event. 
 - The `NotifyAfter` property can be set at any time, even while a bulk copy operation is underway. 
