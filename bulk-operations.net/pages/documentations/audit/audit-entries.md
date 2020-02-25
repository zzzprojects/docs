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
using (var bulk = new BulkOperation(connection))
{
    bulk.DestinationTableName = "Customer";
    bulk.UseAudit = true;
    bulk.BulkMerge(list);
            
    auditEntries = bulk.AuditEntries;
}

// Result
FiddleHelper.WriteTable("1 - AuditEntry", auditEntries);
FiddleHelper.WriteTable("2 - AuditEntryItem", auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/UDHmX6) | [.NET Framework](https://dotnetfiddle.net/nZVvES)

### Result

We outputted all `AuditEntry` and `AuditEntryItem` auditing metadata.
