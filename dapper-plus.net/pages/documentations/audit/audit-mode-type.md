# AuditModeType

## Description

The `AuditModeType` is an enum used to include or exclude all properties in the Audit.

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

We will demonstrate how to include only specific properties by excluding all properties then specifying properties to include.

### Mapping

We will use the following **Mapping**:
- `AuditMode(AuditModeType.ExcludeAll)`: To exclude all properties
- `AuditMode(x => new { x.CustomerID, x.Name }, ColumnMappingAuditModeType.Include)`: To include the `CustomerID` and `Name` properties

### Execute

We will execute a `BulkMerge` on a list that contains **2** existing customers and **1** new customer.

With the following **BulkOptions**:
- [AuditEntries](audit-entries.md): To set the audit entries list
- [UseAudit](use-audit.md): To enable the audit feature

### Result

We will show all `AuditEntry` values. Only information about the `CustomerID` and `Name` will appears.

### Code

```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer")
	.AuditMode(AuditModeType.ExcludeAll)
	.AuditMode(x => new { x.CustomerID, x.Name }, ColumnMappingAuditModeType.Include);
	
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
	x.AuditEntries = auditEntries; 
	x.UseAudit = true;
})
.BulkMerge(list); 

// Result
FiddleHelper.WriteTable(auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Framework](https://dotnetfiddle.net/CDA98j) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)
