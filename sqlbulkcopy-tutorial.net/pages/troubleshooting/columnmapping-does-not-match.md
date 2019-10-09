# SqlBulkCopy - The given ColumnMapping does not match up with any column in the source or destination.

## Problem

You execute the method WriteToServer, and the following error is thrown:

The given ColumnMapping does not match up with any column in the source or destination.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    using (var sqlBulk = new SqlBulkCopy(connection))
    {
    	// Oops! The destination column is case sensitive, it should be instead "Name"
    	sqlBulk.ColumnMappings.Add("Name", "name");
    	sqlBulk.ColumnMappings.Add("Country", "Country");
    	sqlBulk.ColumnMappings.Add("City", "City");
    
     	sqlBulk.DestinationTableName = "Customers";
     	sqlBulk.WriteToServer(dt);
    }
}
```

[Try it (Invalid name)](https://dotnetfiddle.net/Y7K9Fw)

[Try it (More column in the source)](https://dotnetfiddle.net/WaeUi9)

## Solution

### Cause

- You didn't provide any ColumnMappings, and there is more column in the source than in the destination.
- You provided an invalid column name for the source.
- You provided an invalid column name for the destination.

### Fix

- ENSURE to provide a ColumnMappings
- ENSURE all values for source column name are valid and case sensitive.
- ENSURE all values for destination column name are valid and case sensitive.
- MAKE the source case insensitive

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    // SET ColumnMappings values.
    sqlBulk.ColumnMappings.Add("Name", "Name");
    sqlBulk.ColumnMappings.Add("Country", "Country");
    sqlBulk.ColumnMappings.Add("City", "City");
    
    sqlBulk.DestinationTableName = "Customers";
    sqlBulk.WriteToServer(dt);
}
```

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    // SET ColumnMappings values.
    sqlBulk.ColumnMappings.Add("Name", "Name");
    sqlBulk.ColumnMappings.Add("Country", "Country");
    sqlBulk.ColumnMappings.Add("City", "City");
    
    sqlBulk.DestinationTableName = "Customers";
    sqlBulk.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/Zry2tb)

> You cannot make the destination column name case insensitive.

```csharp
var dt = new DataTable();
dt.CaseSensitive = false;
dt.Columns.Add("TheColumnInt", typeof(int));
dt.Columns.Add("TheColumnString");
```
