# AuditEntries

## Description

The `AuditEntries` property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values.

This option requires to set the [UseAudit](use-audit.md) property to `true`.

```csharp
/// <summary>Gets or sets the `AuditEntries` property. The `AuditEntries` property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values. This option requires to set the [UseAudit](use-audit.md) property to `true`.</summary>
public bool AuditEntries { get; set; }
```

## Example

We will demonstrate how to use the `UseAudit` and `AuditEntries` properties.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer, and **2** existing customers.

We will use the following BulkOptions:
- UseAudit: To enable the auditing feature.
- AuditEntries: To retrieve auditing metadata.

### Code

```csharp
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(options => 
{ 
    options.UseAudit = true;
    options.AuditEntries = auditEntries; 
})
.BulkMerge(list); 

// Result
FiddleHelper.WriteTable("1 - AuditEntry", auditEntries);
FiddleHelper.WriteTable("2 - AuditEntryItem", auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/y4w1ZG) | [.NET Framework](https://dotnetfiddle.net/XB5npF)

### Result

We outputted all `AuditEntry` and `AuditEntryItem` auditing metadata.