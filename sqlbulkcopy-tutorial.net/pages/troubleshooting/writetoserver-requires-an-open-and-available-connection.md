# SqlBulkCopy - WriteToServer requires an open and available Connection. The connection's current state is closed.

## Problem

You execute the method WriteToServer, and the following error is thrown:

> WriteToServer requires an open and available Connection. The connection\'s current state is closed.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    // Oops! The connection has never been opened
    //connection.Open();

    using (var sqlBulk = new SqlBulkCopy(connection))
    {		
     	sqlBulk.DestinationTableName = "Customers";
     	sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/dHA0o5)
## Solution

### Cause

- The connection is closed before calling WriteToServer or it was never open.

### Fix

- ENSURE to open the connection

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    // Make sure that connection is open
    connection.Open();

    using (var sqlBulk = new SqlBulkCopy(connection))
    {		
     	sqlBulk.DestinationTableName = "Customers";
     	sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/dpxGBG)