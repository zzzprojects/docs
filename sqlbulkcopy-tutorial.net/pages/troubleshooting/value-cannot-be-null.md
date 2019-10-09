# SqlBulkCopy - Value cannot be null.

## Problem

You execute the method WriteToServer, and the following error is thrown:

> Value cannot be null.

```csharp
// Oops! The destination name is null
string destinationName = null;

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

[Try it](https://dotnetfiddle.net/ukl9us)

## Solution

### Cause

- You provided a null value to the DestinationTableName property.

### Fix

- ENSURE the value you provided is not null.

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

[Try it](https://dotnetfiddle.net/hI0Aaw)
