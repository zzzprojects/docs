# Audit

## Description

The Bulk Operations audit feature allows you to track changes of all modifications that happened in the database during the Bulk Operations. Also, it lets you create a history of who modified a table, what was an old value and a new value.

## Key Features

- Allow getting a history of modification
- Allow storing the history in a database or log file
- Allow tracking who, what, when a modification occurred
- Allow knowing what the old and the new value are

## Getting Started

To use the audit feature, you need to enable it and provide a list of the AuditEntry that will be populated during the operations.

```csharp
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>();
using (var bulk = new BulkOperation(connection))
{
    bulk.DestinationTableName = "Customer";
    bulk.UseAudit = true;
    bulk.BulkMerge(list);
            
    auditEntries = bulk.AuditEntries;
}
```

Try it: [.NET Core](https://dotnetfiddle.net/8GlI8t) | [.NET Framework](https://dotnetfiddle.net/sX9PaM)

## Scenarios

- [Saving audit history in a database](/scenarios/save-audit-history-in-a-database.md)
- Saving the audit history in a log file

## Options

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[UseAudit](audit/use-audit.md)  | Gets or sets the `UseAudit` property. When the `UseAudit` property is `true`, the [AuditEntries](audit/audit-entries.md) property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values. |
|[AuditEntries](audit/audit-entries.md)  | Gets or sets the `AuditEntries` property. The `AuditEntries` property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values. This option requires to set the [UseAudit](audit/use-audit.md) property to `true`. |

## Methods

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[AuditMode](audit/audit-mode.md)  | The `AuditMode` method allows you to exclude or include properties from the auditing. |

## Entities

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[AuditActionType](audit/audit-action-type.md) | The `AuditActionType` enum represents the action that has been performed (Delete, Insert or Update). |
|[AuditEntry](audit/audit-entry.md) | The `AuditEntry` class represents the auditing row metadata that has been modified. |
|[AuditEntryItem](audit/audit-entry-item.md) | The `AuditEntryItem` class represents the auditing value metadata of a row that has been modified. |
|[AuditModeType](audit/audit-mode-type.md) | The `AuditModeType` enum represents if all properties should be included or excluded from the auditing. The default value is `AuditModeType.IncludeAll`. |
|[ColumnMappingAuditModeType](audit/column-mapping-audit-mode-type.md) | The `ColumnMappingAuditModeType` enum represents if a specific property should be included or excluded from the auditing. The default value is `ColumnMappingAuditModeType.Inherit`. |

## FAQ

### Why enabling this option decreases the performance?

When enabling this option, additional SQL are required such as returning all old and new values.
