# AuditMode

## Description

The `AuditMode` method allows you to exclude or include properties from the auditing.

```csharp
namespace Z.Dapper.Plus
{
    public partial class DapperPlusEntityMapper<T>
    {
        /// <summary>The `AuditMode` method allows you to exclude or include properties from the auditing.</summary>
        /// <param name="copyFromConfiguration">The DapperPlusEntityMapper&lt;T&gt; to copy from.</param>
        /// <returns>The DapperPlusEntityMapper&lt;T&gt;.</returns>
        public DapperPlusEntityMapper<T> AuditMode(DapperPlusEntityMapper<T> copyFromConfiguration)
        {
            // ...code...
        }

        /// <summary>The `AuditMode` method allows you to exclude or include properties from the auditing.</summary>
        /// <param name="auditMode">The `AuditModeType` enum represents if all properties should be included or excluded from the auditing.</param>
        /// <returns>The DapperPlusEntityMapper&lt;T&gt;.</returns>
        public DapperPlusEntityMapper<T> AuditMode(AuditModeType auditMode)
        {
            // ...code...
        }

        /// <summary>The `AuditMode` method allows you to exclude or include properties from the auditing.</summary>
        /// <param name="selectors">The properties selectors to exclude or include.</param>
        /// <param name="columnMappingAuditMode">The `ColumnMappingAuditModeType` enum represents if a specific property should be included or excluded from the auditing.</param>
        /// <returns>The DapperPlusEntityMapper&lt;T&gt;.</returns>
        public DapperPlusEntityMapper<T> AuditMode(Expression<Func<T, object>> selectors, ColumnMappingAuditModeType columnMappingAuditMode)
        {
            // ...code...
        } 
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

Try it: [.NET Core](https://dotnetfiddle.net/ezJ9Iu) | [.NET Framework](https://dotnetfiddle.net/BtWOMy)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.