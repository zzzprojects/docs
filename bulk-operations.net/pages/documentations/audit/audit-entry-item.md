---
Name: AuditEntryItem
---

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

        /// <summary>Gets or sets the `NewValue` property. The `NewValue` is the actual value after an insert or update operation is executed.</summary>
        public object NewValue { get; set; }

        /// <summary>Gets or sets the `OldValue` property. The `OldValue` is the previous value before an update or delete operation is executed.</summary>
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

Try it: [.NET Core](https://dotnetfiddle.net/0OP7LP) | [.NET Framework](https://dotnetfiddle.net/lHUfBt)

### Result

We outputted all `AuditEntry` and `AuditEntryItem` auditing metadata.
