# AuditModeType

## Description

The `AuditModeType` enum represents if all properties should be included or excluded from the auditing. The default value is `AuditModeType.IncludeAll`.

You can include or exclude specific properties with the [ColumnMappingAuditModeType](column-mapping-audit-mode-type.md) enum.

```csharp
// The namespace is different because the https://bulk-operations.net/ library is used under the hood..
namespace Z.BulkOperations
{
    /// <summary>The `AuditModeType` enum represents if all properties should be included or excluded from the auditing. The default value is `AuditModeType.IncludeAll`.</summary>
    public enum AuditModeType
    {
        /// <summary>The name/value that represents if all entity properties are included (Default Value).</summary>
        IncludeAll = 0,
		
        /// <summary>The name/value that represents if all entity properties are excluded.</summary>
        ExcludeAll = 1
    }
}
```

## Example

We will demonstrate how to exclude all properties to include only specific properties.

### Mapping

We will use the following mapping:

- `AuditMode(AuditModeType.ExcludeAll)`: To exclude all properties.
- `AuditMode(x => new { x.CustomerID, x.Name }, ColumnMappingAuditModeType.Include)`: To include specific properties.

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
connection.UseBulkOptions(options => 
{ 
    options.UseAudit = true;
    options.AuditEntries = auditEntries; 
})
.BulkMerge(list); 

// Result
FiddleHelper.WriteTable(auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/1tKhnW) | [.NET Framework](https://dotnetfiddle.net/CDA98j)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.
