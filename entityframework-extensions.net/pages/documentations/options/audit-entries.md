---
Name: AuditEntries
LastMod: 2025-06-23
---

# AuditEntries

## Description

The `AuditEntries` property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values.

This option requires setting the [UseAudit](use-audit.md) property to `true`.

```csharp
/// <summary>Gets or sets the `AuditEntries` property. The `AuditEntries` property stores auditing metadata about `INSERTED`, `UPDATED`, and `DELETED` rows and values. This option requires setting the [UseAudit](use-audit.md) property to `true`.</summary>
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
context.BulkMerge(list, options =>
{
    options.UseAudit = true;
    options.AuditEntries = auditEntries;
});

// Result
FiddleHelper.WriteTable("1 - AuditEntry", auditEntries);
FiddleHelper.WriteTable("2 - AuditEntryItem", auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/) | [.NET Framework](https://dotnetfiddle.net/)

### Result

We output all `AuditEntry` and `AuditEntryItem` auditing metadata.
