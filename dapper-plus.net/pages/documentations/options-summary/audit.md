# Audit

## Description
The Dapper Plus audit feature lets you track changes of all modifications that happened in the database during the bulk operations. You can create after a history of who modified a table, what was an old value, what was the new value.

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

Try it: [.NET Framework](https://dotnetfiddle.net/s8wLF9) | [.NET Core](https://dotnetfiddle.net/cjasQV)

## Options
| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[UseAudit](../options/use-audit.md)  | Gets or sets if the `Audit` feature is enabled |
|[AuditEntries](../options/audit-entries.md)  | Gets or sets the list of `AuditEntries` that will be populated during the bulk operations |
|[AuditMode](../options/audit-mode.md)  | Gets or sets which properties/columns should be included/excluded. |

## Entities
| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[AuditActionType](../options/audit-entities.md#audit-action-type) | The  |
|[AuditEntry](../options/audit-entities.md#audit-entry) | The `AuditEntry` class definition |
|[AuditEntryItem](../options/audit-entities.md#audit-entry-item) | The `AuditEntryItem` class definition |
