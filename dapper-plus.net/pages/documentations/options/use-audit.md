# UseAudit

## Definition

The `UseAudit` property sets if `UPDATED`, `INSERTED` and `DELETED` data from the database should be returned as `AuditEntries`.

The following example sets `UseAudit` to `true` and assigns the list of `AuditEntries` to populate.

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

## Purpose
Logging old and new values is often useful to keep a history of changes in the database or file.

## FAQ

### Why enabling this option decreases the performance?
Enabling this option will require additional data to be returned from the database.
