---
Name: The DestinationTableName property must be set before calling this method
---

# SqlBulkCopy - The DestinationTableName property must be set before calling this method.

## Problem

You execute the method WriteToServer, and the following error is thrown:

> The DestinationTableName property must be set before calling this method.

```csharp
// Oops! The destination name is empty
string destinationName = "";

using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    using (var sqlBulk = new SqlBulkCopy(connection))
    {
        sqlBulk.DestinationTableName = destinationName;
        sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/6H5MhC)

## Solution

### Cause

- You never provided any value to the DestinationTableName property (empty by default).
- You provided an empty string to the DestinationTableName property.

### Fix

- ENSURE you provide value to the DestinationTableName property.
- ENSURE the value you provided is not empty.

```csharp
string destinationName = "Customers";

using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    using (var sqlBulk = new SqlBulkCopy(connection))
    {
        sqlBulk.DestinationTableName = destinationName;
        sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/lAbVe0)