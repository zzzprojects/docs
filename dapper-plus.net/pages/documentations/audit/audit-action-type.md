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
We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

With the following **BulkOptions**:
- `AuditEntries`: To set the audit entries list
- `UseAudit`: To enable the audit feature

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

### Result

The `AuditEntries` list will contains **3** `AuditEntry`
- The first result table displays audit entries that has been updated
- The second result table displays audit entries that has been inserted


with the `Action` property value set to:

- `AuditActionType.Update = 2`: For the **2** existing customers
- `AuditActionType.Insert = 1`: For the **1** new customer

The first result table only display audit entries that has been updated. The second result table only display audit entries that has been inserted.

