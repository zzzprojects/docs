# UseAudit

## Description

The `UseAudit` property sets if `UPDATED`, `INSERTED` and `DELETED` data from the database should be returned as `AuditEntries`.

## Example

In this example,

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