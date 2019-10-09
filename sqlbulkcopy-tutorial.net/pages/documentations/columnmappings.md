# SqlBulkCopy - ColumnMappings

## Description

Column mappings define the relationships between columns in the data source and columns in the destination.

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

## Remarks

 - If the data source and the destination table have the same number of columns, and the ordinal position, the ColumnMappings collection is unnecessary. 
 - However, if the column counts differ, or the ordinal positions are not consistent, you must use ColumnMappings to make sure that data is copied into the correct columns.

## Recommendation
- Always map column explicitly.

### Always map column explicitly
The default mapping under the hood of SqlBulkCopy may cause some weird issue because column are not mapped by name but by ordinal. When your table containt an identity value, you may end up by value mapped to wrong column!

See: [SqlBulkCopy giving FOREIGN KEY constraint error](http://stackoverflow.com/a/39728073/5619143)