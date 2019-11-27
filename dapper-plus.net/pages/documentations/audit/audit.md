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
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer");
                
// Connection
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
        
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list); 

// Result
FiddleHelper.WriteTable("1 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
FiddleHelper.WriteTable("2 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
```

Try it: [.NET Core](https://dotnetfiddle.net/TnH93l) | [.NET Framework](https://dotnetfiddle.net/Yo8cts)

## Scenarios

- Saving the audit history in a database
- Saving the audit history in a log file

## Options

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[UseAudit](use-audit.md)  | Gets or sets if the `Audit` feature is enabled. |
|[AuditEntries](audit-entries.md)  | Gets or sets the list of `AuditEntries` that will be populated during the bulk operations. |
|[AuditMode](audit-mode.md)  | Gets or sets which properties/columns should be included/excluded. |

## Entities

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[AuditActionType](audit-action-type.md) | The `AuditActionType` enum definition.  |
|[AuditEntry](audit-entry.md) | The `AuditEntry` class definition. |
|[AuditEntryItem](audit-entry-item.md) | The `AuditEntryItem` class definition. |
|[AuditModeType](audit-mode-type.md) | The `AuditEntryItem` class definition. |
|[ColumnMappingAuditModeType](column-mapping-audit-mode-type.md) | The `AuditEntryItem` class definition. |

## FAQ

### Why enabling this option decreases the performance?

Enabling this option will require additional data to be returned from the database.
