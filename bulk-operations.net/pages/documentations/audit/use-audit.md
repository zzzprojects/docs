---
Name: UseAudit
---

# UseAudit

## Description

When the `UseAudit` property is `true`, the [AuditEntries](audit-entries.md) property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values.

```csharp
/// <summary>Gets or sets the `UseAudit` property. When the `UseAudit` property is `true`, the [AuditEntries](audit-entries.md) property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values.</summary>
public bool UseAudit { get; set; }
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

Try it: [.NET Core](https://dotnetfiddle.net/7juAFj) | [.NET Framework](https://dotnetfiddle.net/ZRSJEb)

### Result

We outputted all `AuditEntry` and `AuditEntryItem` auditing metadata.
