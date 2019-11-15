# AuditActionType

## Description

The `AuditActionType` is an enum used for the `Action` property in the [AuditEntry](audit-entry.md) class.

It represents the SQL operation executed in the database.

```csharp
// The https://bulk-operations.net/ library is used under the hood
namespace Z.BulkOperations
{
    /// <summary>The action representing the SQL operation executed in the database.</summary>
    public enum AuditActionType
    {
        /// <summary>The name/value when a DELETE operation is executed.</summary>
        Delete = 0,

        /// <summary>The action value when a DELETE operation is executed.</summary>
        Insert = 1,

        /// <summary>An enum constant representing the update operation.</summary>
        Update = 2
    }
}
```

## Example

We will demonstrate an example that 

how the `Action` property is populated.

### Execute
We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

With the following **BulkOptions**:
- `UseAudit`: `True` to enable the audit feature
- `AuditEntries`: To retrieve audit entries

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
FiddleHelper.WriteTable(auditEntries);
```

Try it: [.NET Framework](https://dotnetfiddle.net/WTIe5L) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)

### Result

The `AuditEntries` property will contains **3** `AuditEntry` with the `Action` property value set to:

- `1 = AuditActionType.Insert`: For the **1** new customer
- `2 = AuditActionType.Update`: For the **2** existing customers
