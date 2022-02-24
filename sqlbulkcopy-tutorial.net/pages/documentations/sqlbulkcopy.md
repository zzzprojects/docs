---
Name: SqlBulkCopy Class
---

# SqlBulkCopy Class

## Description
SqlBulkCopy is the **FASTEST** way to insert multiples rows from a data source to a SQL Server/Azure.

```csharp
using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
{
    // SET BatchSize value.
    bulkCopy.BatchSize = 50;

    bulkCopy.DestinationTableName = "TheDestinationTable";
    bulkCopy.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/JomX1U)

## Options

| Name | Description |
| :--- | :---------- |
| <a href="{{ site.github.url }}/sqlbulkcopyoptions">SqlBulkCopyOptions</a> | Bitwise flag to specifies one or more options. |

## Properties

| Name | Description |
| :--- | :---------- |
| <a href="{{ site.github.url }}/BatchSize">batchsize</a> | Number of rows for a batch. |
| <a href="{{ site.github.url }}/bulkcopytimeout">BulkCopyTimeout</a> | Number of seconds maximum a batch can take before it times out. |
| <a href="{{ site.github.url }}/columnmappings">ColumnMappings</a> | Mapping between the source and destination columns. |
| <a href="{{ site.github.url }}/destinationtablename">DestinationTableName</a> | Name of the destination table. |
| <a href="{{ site.github.url }}/enablestreaming">EnableStreaming</a> | True to enable streaming for IDataReader object. |
| <a href="{{ site.github.url }}/notifyafter">NotifyAfter</a> | Number of rows to process before raising the SqlRowsCopied events. |

## Methods

| Name | Description |
| :--- | :---------- |
| <a href="{{ site.github.url }}/writetoserver">WriteToServer</a> | Copy all rows from the source to the destination table. |
| <a href="{{ site.github.url }}/writetoserverasync">WriteToServerAsync</a> | The asynchronous version of WriteToServer. Copy all rows from the source to the destination table. |

## Events

| Name | Description |
| :--- | :---------- |
| <a href="{{ site.github.url }}/sqlbulkcopyoptions">SqlRowsCopied</a> | Event raised after the number of row in the NotifyAfter has been processed. |