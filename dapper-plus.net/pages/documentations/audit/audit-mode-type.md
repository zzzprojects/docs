# AuditModeType

## Description

The `AuditModeType` is an enum that represents all entity properties are included or excluded.

The default value is `AuditModeType.IncludeAll`.

```csharp
// The https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The enum that represents all entity properties are included or excluded.</summary>
    public enum AuditModeType
    {
        /// <summary>The name/value that represents all entity properties are included.</summary>
        IncludeAll = 0,
		
        /// <summary>The name/value that represents all entity properties are excluded.</summary>
        ExcludeAll = 1
    }
}
```

## Example

We will demonstrate how to include specific entity properties.

### Mapping

We will use the following mapping:

- `AuditMode(AuditModeType.ExcludeAll)`: To exclude all entity properties
- `AuditMode(x => new { x.CustomerID, x.Name }, ColumnMappingAuditModeType.Include)`: To include specific properties

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

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

Try it: [.NET Core](https://dotnetfiddle.net/1tKhnW) | [.NET Framework](https://dotnetfiddle.net/CDA98j)

### Result

We will show all `AuditEntry` values. The only information that appears is about the `CustomerID` and `Name` property.
