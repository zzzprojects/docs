---
Name: BulkCopyTimeout
---

# SqlBulkCopy - BulkCopyTimeout

## Description

Number of seconds maximum for a batch before it times out.

- Type: System.Int32
- Default Value: 30 
- Note: A value of 0 indicates the SqlBulkCopy can wait forever

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    // SET BulkCopyTimeout value.
    sqlBulk.BulkCopyTimeout = 300;
    
    sqlBulk.DestinationTableName = "Customers";
    sqlBulk.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/7vyG79)

## Recommendation
- Higher is better!

### Higher is better!
 - Recommended BulkCopyTimeout Value: **300**
 
Ensure to set the BulkCopyTimeout to at least 3x of the time the SqlBulkCopy need to complete the transaction.

If the operation may take up to 20s, specify a value of at least of 60s.

You don't know in one year what could happen! A file importation that started small may have grown to millions of rows. There is nothing worse than an application crashing in production because of a Timeout exception occurred!