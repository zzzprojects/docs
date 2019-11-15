# AuditModeType

## Description

The `AuditModeType` is an enum used to include or exclude all properties from the Audit.

The default value is `AuditModeType.IncludeAll`.

```csharp
// The https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The enum that represents if properties are included or excluded.</summary>
    public enum AuditModeType
    {
        /// <summary>The name/value that represents if all properties are included.</summary>
        IncludeAll = 0,
		
        /// <summary>The name/value that represents if all properties are excluded.</summary>
        ExcludeAll = 1
    }
}
```

## Example

We will demonstrate how to include only specific properties.

### Execute

We will execute a `BulkMerge` on a list that contains **2** existing customers and **1** new customer.

With the following **Mapping**:

- `AuditMode(AuditModeType.ExcludeAll)`: To exclude all properties from the auditing
- `AuditMode(AuditModeType.Include, x => new { x.CustomerID, x.Code })`: To include the `CustomerID` and `Code` properties in the auditing.

With the following **BulkOptions**:
- [AuditEntries](audit-entries.md): To set the audit entries list
- [UseAudit](use-audit.md): To enable the audit feature

### Result

...
The `AuditEntries` property will contains **3** `AuditEntry`. The `Values` property will only contains the `CustomerID` and `Code` properties, all other properties are ignored.

We will split the `AuditEntries` list by their `AuditActionType` value.

### Code

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
