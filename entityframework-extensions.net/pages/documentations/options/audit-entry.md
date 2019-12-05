# AuditEntry

## Description

The `AuditEntry` class represents the auditing row metadata that has been modified.

The auditing value metadata is a list of [AuditEntryItem](audit-entry-item.md) class.

```csharp
// The namespace is different because the https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The `AuditEntry` class represents the auditing row metadata that has been modified. The auditing value metadata is a list of [AuditEntryItem](audit-entry-item.md) class.</summary>
    public class AuditEntry
    {
        /// <summary>Gets or sets the `Action` property.</summary>
        public AuditActionType Action { get; set; }
        
        /// <summary>Gets or sets the `Date` property.</summary>
        public DateTime Date { get; set; }

        /// <summary>Gets or sets the `Metas` property.</summary>
        public Dictionary<object, object> Metas { get; set; }

        /// <summary>Gets or sets the `TableName` property..</summary>
        public string TableName { get; set; }

        /// <summary>Gets or sets the `Values` property.</summary>
        public List<AuditEntryItem> Values { get; set; }
    }
}
```

## Example

We will demonstrate how to retrieve `AuditEntry`.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

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

We outputted all `AuditEntry` and `AuditEntryItem` auditing metadata.
