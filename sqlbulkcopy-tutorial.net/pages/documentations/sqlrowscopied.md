# SqlBulkCopy - SqlRowCopied

## Description
The `SqlRowCopied` event raised after the number of row in the NotifyAfter has been processed.

## Example

The following example sets `NotifyAfter` property so that the event handler is called after every 200 rows copied to the table.

```csharp
using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connectionString))
{
    // SET SqlRowsCopied event.
    bulkCopy.SqlRowsCopied += (sender, args) => { /* code */ }; 
    
    bulkCopy.NotifyAfter = 4000;

    bulkCopy.DestinationTableName = "TheDestinationTable";
    bulkCopy.WriteToServer(reader);
}
```

[Try it](https://dotnetfiddle.net/fSjawf)

## Remarks

The receipt of a SqlRowsCopied event does not imply that any rows have been sent to the server or committed.

 - You cannot call `SqlBulkCopy.Close` or `SqlConnection.Close` from this event, if do so, it will throw an `InvalidOperationException` exception, and the SqlBulkCopy object state will not change. 
 - If you want to cancel the operation from the event, use the `Abort` property of the `SqlRowsCopiedEventArgs`. 