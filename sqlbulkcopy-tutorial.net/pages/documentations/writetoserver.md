# SqlBulkCopy - WriteToServer

## Description

Copy all rows from the source to the destination table. It accepts an array of DataRows or DataTable or IDataReader.

```csharp
using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connectionString))
{
    bulkCopy.DestinationTableName = "TheDestinationTable";
    
    // CALL WriteToServer method.
    bulkCopy.WriteToServer(dt);
}
```
[Try it](https://dotnetfiddle.net/mjuhri)

## WriteToServer(DataTable, DataRowState)

Copies only rows that match the supplied row state in the supplied `DataTable` to a destination table. The following example bulk load only the rows in a `DataTable` that match a specified state. In this case, only unchanged rows are added.

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    // Make a change to one of the rows in the DataTable.
    DataRow row = dt.Rows[0];
    row.BeginEdit();
    row["Name"] = "AAA";
    row.EndEdit();
    sqlBulk.DestinationTableName = "Customers";
    
    // CALL WriteToServer method.
    sqlBulk.WriteToServer(dt, DataRowState.Unchanged);
}
```

[Try it](https://dotnetfiddle.net/asasFF)

## WriteToServer(IDataReader)

Copies all rows in the supplied `IDataReader` to a destination table. The following example bulk load data from a **"Customers_Archive"** table using `SqlDataReader` and write that data to the **"Customers"** table by passing the `SqlDataReader` object to `WriteToServer` method.

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();

    // Get data from the source table as a SqlDataReader.
    SqlCommand commandSourceData = new SqlCommand(
        "SELECT * FROM Customers_Archive;", connection);

    SqlDataReader reader =
        commandSourceData.ExecuteReader();

    using (var dConnection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
    {
        dConnection.Open();
        using (var sqlBulk = new SqlBulkCopy(dConnection))
        {
            sqlBulk.DestinationTableName = "Customers";
            try
            {
                // Write from the source to the destination.
                sqlBulk.WriteToServer(reader);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            finally
            {
                reader.Close();
            }
        }
    }
}
```
[Try it](https://dotnetfiddle.net/uVaDcC)

## WriteToServer(DataRow[])

Copies all rows from the supplied DataRow array to a destination table.

```csharp
using (var sqlBulk = new SqlBulkCopy(connection))
{
    DataRow[] dataRows = dt.Select();
    sqlBulk.DestinationTableName = "Customers";
    
    // CALL WriteToServer method.
    sqlBulk.WriteToServer(dataRows);
}
```
[Try it](https://dotnetfiddle.net/hlde72)