# ColumnMappingAuditModeType

## Description

The `AuditActionType` is a property of the `ColumnMapping` class. 

The default value is `Inherit`.

Inherit: Inherit from the AuditMode from the `BulkOperation` class. When IncludeAll, this column is included; When ExcludeAll, this column is excluded.
Include: The column is always included in the audit.
Exclude: The column is always excluded from the audit.

```csharp
// The https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>Values that represent ColumnMappingAuditModeType.</summary>
    public enum ColumnMappingAuditModeType
    {
        /// <summary>An enum constant representing the inherit option.</summary>
        Inherit,

        /// <summary>An enum constant representing the include option.</summary>
        Include,

        /// <summary>An enum constant representing the exclude option.</summary>
        Exclude
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

A BulkMerge will be performed. All columns are included by default but we explicit exclude a `Description` and `IsActive` columns.

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

Try it: [.NET Core](https://dotnetfiddle.net/AmxN6Z) | [.NET Framework](https://dotnetfiddle.net/ANSXt4)

### Result

We outputted all `AuditEntry` values. The only information that appears is about the `CustomerID` and `Name` property.
