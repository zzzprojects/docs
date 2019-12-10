# ColumnMappingAuditModeType

## Description

The `ColumnMappingAuditModeType` enum represents if a specific property should be included or excluded from the auditing. The default value is `ColumnMappingAuditModeType.Inherit`.

You can include or exclude all properties with the `Inherit` value using the [AuditModeType](audit-mode-type.md) enum.

```csharp
// The namespace is different because the https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The `ColumnMappingAuditModeType` enum represents if a specific property should be included or excluded from the auditing. The default value is `ColumnMappingAuditModeType.Inherit`.</summary>
    public enum ColumnMappingAuditModeType
    {
        /// <summary>The name/value that represents if a specific property inherit from the AuditModeType (Default Value).</summary>
        Inherit,

        /// <summary>The name/value that represents if a specific property is included.</summary>
        Include,

        /// <summary>The name/value that represents if a specific property is excluded.</summary>
        Exclude
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

Try it: [.NET Core](https://dotnetfiddle.net/AmxN6Z) | [.NET Framework](https://dotnetfiddle.net/ANSXt4)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.