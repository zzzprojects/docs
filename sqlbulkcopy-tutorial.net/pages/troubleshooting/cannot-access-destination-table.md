# SqlBulkCopy - Cannot access destination table '[TheInvalidTableName]'.

## Problem

You execute the method WriteToServer, and the following error is thrown:

> Cannot access destination table '[TheInvalidTableName]'.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    using (var sqlBulk = new SqlBulkCopy(connection))
    {
        // Oops! The destination name is Customer instead of Customers 		
        sqlBulk.DestinationTableName = "Customer";
        sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/Fmso3s)

## Solution

### Cause

- You provided an invalid schema name.
- You provided an invalid table name.
- The user doesn't have the right to the schema name.
- The user doesn't have the right to the table name.

### Fix

- ENSURE the schema name is valid.
- ENSURE the table name is valid.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    using (var sqlBulk = new SqlBulkCopy(connection))
    {		
        sqlBulk.DestinationTableName = "Customers";
        sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/S7HL4P)

If the schema and table name was already valid:

- LOG into SSMS with the user/password used by the application and perform a SELECT statement to ensure he has access to it.
