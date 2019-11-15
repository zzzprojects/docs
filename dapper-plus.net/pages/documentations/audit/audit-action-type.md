# AuditActionType

## Description

The `AuditActionType` is a property of the `AuditEntry` class.

It represent the SQL action that has been performed for the entry:
- Delete
- Insert
- Update

```csharp
namespace Z.BulkOperations
{
    /// <summary>Values that represent AuditActionType.</summary>
    public enum AuditActionType
    {
        /// <summary>An enum constant representing the delete option.</summary>
        Delete = 0,

        /// <summary>An enum constant representing the insert option.</summary>
        Insert = 1,

        /// <summary>An enum constant representing the update option.</summary>
        Update = 2
    }
}
```

> HINT: The `AuditActionType` uses the Z.BulkOperations namespace since the library is used under the hood.

## Example

We will demonstrate how the `Action` property is populated.

### Execute
We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

**BulkOptions**:
- `UseAudit`: To enable the audit feature
- `AuditEntries`: To retrieve audit entries

```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer");

// BulkOptions
connection.UseBulkOptions(x => 
{ 
	x.AuditEntries = auditEntries; 
	x.UseAudit = true;
})
.BulkMerge(list);
```

Try it: [.NET Framework](https://dotnetfiddle.net/WTIe5L) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)

### Result

The `AuditEntries` property will contains **3** `AuditEntry` with the `Action` property value set to:

- `1 = AuditActionType.Insert`: For the **1** new customer
- `2 = AuditActionType.Update`: For the **2** existing customers
