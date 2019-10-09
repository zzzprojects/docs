# SqlBulkCopy - The given value of type String from the data source cannot be converted to type int of the specified target column.

## Problem

You execute the method WriteToServer, and the following error is thrown:

> The given value of type String from the data source cannot be converted to type int of the specified target column.

```csharp
var dt = new DataTable();
dt.Columns.Add("CustomerID", typeof(int));
dt.Columns.Add("Name");
dt.Columns.Add("Age");
			   
for(int i = 0; i < 1000; i++)
{
    dt.Rows.Add(i, "Name_" + i, "");
}	

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

[Try it](https://dotnetfiddle.net/T2EQIb)

## Solution

### Cause

- You provided an empty value which cannot be converted to another type.

### Fix

- ENSURE provided value is valid for the destination type.
- CREATE column by specifying the type to get the error before executing WriteToServer method.

### Example

```csharp
var dt = new DataTable();
dt.Columns.Add("CustomerID", typeof(int));
dt.Columns.Add("Name", typeof(string));
dt.Columns.Add("Age", typeof(int));
			   
for(int i = 0; i < 1000; i++)
{
    dt.Rows.Add(i, "Name_" + i, i);
}	

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

[Try it](https://dotnetfiddle.net/fkyMNz)