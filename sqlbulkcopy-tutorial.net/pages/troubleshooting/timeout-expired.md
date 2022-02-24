---
Name: Execution Timeout Expired. The timeout period elapsed prior to completion of the operation or the server is not responding
---

# SqlBulkCopy - Execution Timeout Expired. The timeout period elapsed prior to completion of the operation or the server is not responding.

## Problem

You execute the method WriteToServer, and the following error is thrown:

> Execution Timeout Expired. The timeout period elapsed prior to completion of the operation or the server is not responding.

```csharp
// Oops! The specified bulkCopyTimeout value is to low!
bulkCopyTimeout = 1;

using (var connection = new SqlConnection(My.Config.ConnectionStrings.BulkOperations))
{
    connection.Open();

    using (var bulkCopy = new SqlBulkCopy(connection))
    {
        // SET BulkCopyTimeout value.
        bulkCopy.BulkCopyTimeout = bulkCopyTimeout;
        
        bulkCopy.DestinationTableName = destinationName;
        bulkCopy.WriteToServer(dt);
    }
}
```

## Solution

### Cause

- The bulk copy take more time to execute than the BulkCopyTimeout specified.

### Fix

- INCREASE the BulkCopyTimeout value (default = 30s).
- DECREASE the BatchSize value (default = unlimited).

> The bulkCopyTimeout is by batch, not for the whole bulk operation.