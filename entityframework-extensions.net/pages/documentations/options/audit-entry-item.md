# AuditEntryItem

## Description

The `AuditEntryItem` class represents the auditing value metadata of a row that has been modified.

The entry item is used in the [AuditEntry](audit-entry.md) class.

```csharp
// The namespace is different because the https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The `AuditEntryItem` class represents the auditing value metadata of a row that has been modified. The entry item is used in the [AuditEntry](audit-entry.md) class.</summary>
    public class AuditEntryItem
    {
        /// <summary>Gets or sets the `ColumnName` property.</summary>
        public string ColumnName { get; set; }

        /// <summary>Gets or sets the `NewValue` property. The `NewValue` is the actual value after an insert or an update operation is executed.</summary>
        public object NewValue { get; set; }

        /// <summary>Gets or sets the `OldValue` property. The `OldValue` is the previous value before an update or a delete operation is executed.</summary>
        public object OldValue { get; set; }
    }
}
```

## Example

We will demonstrate how to retrieve `AuditEntryItem`.

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
