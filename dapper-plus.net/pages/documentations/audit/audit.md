# Audit

## Description

The Dapper Plus audit feature lets you track changes of all modifications that happened in the database during the bulk operations. It allow you to create an history of who modified a table, what was an old value, what was the new value.

## Key Features

- Allow getting a history of modification
- Allow storing the history in a database or log file
- Allow tracking who, what, when a modification occurred
- Allow knowing what the old and new value was

## Getting Started

To use the audit feature, you need first to enable it and provide a list if AuditEntry that will be populated during the operations.

```csharp
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list); 
```

Try it: [.NET Core](https://dotnetfiddle.net/TnH93l) | [.NET Framework](https://dotnetfiddle.net/Yo8cts)

## Scenarios

- [Saving audit history in a database](/scenarios/save-audit-history-in-a-database.md)
- Saving the audit history in a log file

## Options

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[UseAudit](use-audit.md)  | Gets or sets the `UseAudit` property. When the `UseAudit` property is `true`, the [AuditEntries](audit-entries.md) property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values. |
|[AuditEntries](audit-entries.md)  | Gets or sets the `AuditEntries` property. The `AuditEntries` property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values. This option requires to set the [UseAudit](use-audit.md) property to `true`. |

## Methods

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[AuditMode](audit-mode.md)  | The `AuditMode` method allows you to exclude or include properties from the auditing. |

## Entities

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[AuditActionType](audit-action-type.md) | The `AuditActionType` enum represents the action that has been performed (Delete, Insert or Update). The action is used in the [AuditEntry](audit-entry.md) class.  |
|[AuditEntry](audit-entry.md) | The `AuditEntry` class represents the auditing row metadata that has been modified. The auditing value metadata is a list of [AuditEntryItem](audit-entry-item.md) class. |
|[AuditEntryItem](audit-entry-item.md) | The `AuditEntryItem` class represents the auditing value metadata of a row that has been modified. The entry item is used in the [AuditEntry](audit-entry.md) class. |
|[AuditModeType](audit-mode-type.md) | The `AuditModeType` enum represents if all properties should be included or excluded from the auditing. The default value is `AuditModeType.IncludeAll`. |
|[ColumnMappingAuditModeType](column-mapping-audit-mode-type.md) | The `ColumnMappingAuditModeType` enum represents if a specific property should be included or excluded from the auditing. The default value is `ColumnMappingAuditModeType.Inherit`. |

## FAQ

### Why enabling this option decreases the performance?

When enabling this options, additional SQL are required such as returning all old and new values.
