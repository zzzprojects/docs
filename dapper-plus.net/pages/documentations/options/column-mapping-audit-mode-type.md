# ColumnMappingAuditModeType

## Description

The `AuditEntries` property which is of type `List<AuditEntry>` gets `UPDATED`, `INSERTED` and `DELETED` data from the database when `UseAudit` is enabled.

The following example sets `UseAudit` to `true` and assigns the list of `AuditEntries` to populate.

```csharp
namespace Z.BulkOperations
{
    /// <summary>Values that represent AuditModeType.</summary>
    public enum AuditModeType
    {
		// the doc
        IncludeAll,
		
		// the doc
        ExcludeAll
    }
}
```
Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)