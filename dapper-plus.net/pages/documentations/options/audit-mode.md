# AuditMode

## Definition

The `AuditMode` property sets to include or exclude all columns from the database which should be returned as `AuditEntries`. By default, all columns are included.

The following example sets `UseAudit` to `true`, assigns the list of `AuditEntries` to populate, and also sets the `AuditMode` to `AuditModeType.ExcludeAll`.

```csharp
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
        
connection.UseBulkOptions(x => 
{ 
    x.UseAudit = true;
    x.AuditEntries = auditEntries;
    x.AuditMode = AuditModeType.ExcludeAll;
    
})
.BulkMerge(list);
```

Try it: [.NET Framework](https://dotnetfiddle.net/ulrLSL) | [.NET Core](https://dotnetfiddle.net/T5MgRa)

The `AuditMode` property can also be set globally instead of only for any particular bulk operation to include or exclude all columns from the database.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers")
    .Identity(x => x.CustomerID)
    .AuditMode(AuditModeType.ExcludeAll);
        
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
        
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list);
```

Try it: [.NET Framework](https://dotnetfiddle.net/BtWOMy) | [.NET Core](https://dotnetfiddle.net/ezJ9Iu)

## Purpose
Logging old and new values are often useful to keep a history of changes in the database or file.

## FAQ

### Why enabling this option decreases the performance?
Enabling this option will require additional data to be returned from the database.
