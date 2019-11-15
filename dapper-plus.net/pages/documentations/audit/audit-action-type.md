# AuditActionType

## Description

The `AuditActionType` is an enum used for the `Action` property in the [AuditEntry](audit-entry.md) class.

It represents the SQL operation executed in the database.

```csharp
// The https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The enum that represents the SQL operation executed in the database.</summary>
    public enum AuditActionType
    {
        /// <summary>The name/value that represents the DELETE operation.</summary>
        Delete = 0,

        /// <summary>The name/value that represents the INSERT operation.</summary>
        Insert = 1,

        /// <summary>The name/value that represents the UPDATE operation.</summary>
        Update = 2
    }
}
```

## Example

We will demonstrate how to split the `AuditEntries` list by their `AuditActionType` value.

### Execute

We will execute a `BulkMerge` on a list that contains **2** existing customers and **1** new customer.

With the following **BulkOptions**:
- [AuditEntries](audit-entries.md): To set the audit entries list
- [UseAudit](use-audit.md): To enable the audit feature

### Result
We will split the `AuditEntries` list by their `AuditActionType` value.

The `AuditEntries` list will be split by their `AuditActionType` value.

Then we split the `AuditEntries` list by their `AuditActionType` value.

```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer");

// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list);

// Result
FiddleHelper.WriteTable("1 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
FiddleHelper.WriteTable("2 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
```

Try it: [.NET Framework](https://dotnetfiddle.net/WTIe5L) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)


