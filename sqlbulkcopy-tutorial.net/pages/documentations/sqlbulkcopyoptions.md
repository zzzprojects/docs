---
Name: SqlBulkCopyOptions
---

# SqlBulkCopy - SqlBulkCopyOptions

## Description
Bitwise flag to specifies one or more options.

```csharp
// SET SqlBulkCopyOptions value.
using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connectionString, SqlBulkCopyOptions.CheckConstraints))
{
	bulkCopy.DestinationTableName = "TheDestinationTable";
	bulkCopy.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/ng64Z4)

## Options

| Name | Default | Description |
| ---- | :-----: | ----------- |
| AllowEncryptedValueModifications | No | Allow bulk copying encrypting data. |
| CheckConstraints | No |  Check constraints while data is being inserted. |
| Default | Yes | Default options = None. |
| FireTriggers | No |  Fire triggers while data is being inserted. |
| KeepIdentity | No | Preserve source identity values. |
| KeepNulls | No | Preserve null values in the destination table regardless of the settings for default values. When not specified, null values are replaced by default values where applicable. |
| TableLock | No | Obtain a bulk update lock for the duration of the bulk copy operation. When not specified, row locks are used. |
| UseInternalTransaction | No | When specified, each batch of the bulk-copy operation will occur within a transaction. |

## Remarks

You can specify more than one value by using the "|" operators (Logical OR).

```csharp
var options = SqlBulkCopyOptions.CheckConstraints | SqlBulkCopyOptions.FireTriggers | SqlBulkCopyOptions.TableLock;

using (var sqlBulk = new SqlBulkCopy(connectionString, options))
{
    sqlBulk.DestinationTableName = "Customers";
    sqlBulk.WriteToServer(dt);
}
```

[Try it](https://dotnetfiddle.net/lWfHtH)

## Recommendation
- SET CheckConstraints ON
- SET FireTriggers ON

### SET CheckConstraints ON
By default, SqlBulkCopy doesn't check constraint.

It's recommended to always check constraint even if your table has none currently.

### SET FireTriggers ON
By default, SqlBulkCopy doesn't check trigger.

It's recommended to always fire trigger even if your table has none currently.