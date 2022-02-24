---
Name: WriteToServerAsync
---

# SqlBulkCopy - WriteToServerAsync

## Description

The asynchronous version of `WriteToServer`. Copy all rows from the source to the destination table. It accepts an array of DataRows or DataTable or IDataReader.


```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    var sqlBulk = new SqlBulkCopy(connection);

    CancellationTokenSource cancelationTokenSource = new CancellationTokenSource();
    CancellationToken cancellationToken = cancelationTokenSource.Token;
    sqlBulk.DestinationTableName = "Customers";

    // Write from the source to the destination asynchronously.
    var task = sqlBulk.WriteToServerAsync(dt, cancellationToken);
}
```

[Try it](https://dotnetfiddle.net/NvWHk8)

## WriteToServerAsync(DataRow[])

The asynchronous version of `WriteToServer(DataRow[])`, which copies all rows from the supplied `DataRow` array to a destination table asynchronously.


```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    var sqlBulk = new SqlBulkCopy(connection);

    DataRow[] dataRows = dt.Select();
    sqlBulk.DestinationTableName = "Customers";

    // Write from the source to the destination asynchronously.
    Task.Run(async () => { await sqlBulk.WriteToServerAsync(dataRows); }).Wait();
    sqlBulk.Close();
}
```
[Try it](https://dotnetfiddle.net/O01ZUP)

## WriteToServerAsync(DataTable)

The asynchronous version of `WriteToServer(DataTable)`, which copies all rows in the supplied DataTable to a destination table asynchronously.

```csharp
using(var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();
    var sqlBulk = new SqlBulkCopy(connection);

    sqlBulk.DestinationTableName = "Customers";
    
    // Write from the source to the destination asynchronously.
    Task.Run(async () => { await sqlBulk.WriteToServerAsync(dt); }).Wait();
    sqlBulk.Close();
}
```

[Try it](https://dotnetfiddle.net/Px1f03)

## WriteToServerAsync(IDataReader)

The asynchronous version of WriteToServer(IDataReader), which copies all rows in the supplied IDataReader to a destination table asynchronously.

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
                // Write from the source to the destination asynchronously.
                Task.Run(async () => { await sqlBulk.WriteToServerAsync(reader); }).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            finally
            {
                sqlBulk.Close();
                reader.Close();
            }
        }
    }
}
```

[Try it](https://dotnetfiddle.net/FBz0sv)