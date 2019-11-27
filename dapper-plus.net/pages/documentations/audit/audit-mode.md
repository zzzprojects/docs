# AuditMode

## Description

The `AuditMode` is a method to exclude or include property that will be part of the auditing

```csharp
namespace Z.Dapper.Plus
{
    public partial class DapperPlusEntityMapper<T>
    {
        /// <summary>Audit mode column.</summary>
        /// <param name="copyFromConfiguration">The copy from configuration.</param>
        /// <returns>A DapperPlusEntityMapper&lt;T&gt;</returns>
        public DapperPlusEntityMapper<T> AuditMode(DapperPlusEntityMapper<T> copyFromConfiguration)
        {
            // ...code...
        }

        /// <summary>Audit mode.</summary>
        /// <param name="auditMode">The audit mode.</param>
        /// <returns>A DapperPlusEntityMapper&lt;T&gt;</returns>
        public DapperPlusEntityMapper<T> AuditMode(AuditModeType auditMode)
        {
            // ...code...
        }

        /// <summary>Audit mode column.</summary>
        /// <param name="selectors">The selectors.</param>
        /// <param name="columnMappingAuditMode">The column mapping audit mode.</param>
        /// <returns>A DapperPlusEntityMapper&lt;T&gt;</returns>
        public DapperPlusEntityMapper<T> AuditMode(Expression<Func<T, object>> selectors, ColumnMappingAuditModeType columnMappingAuditMode)
        {
            // ...code...
        } 
    }
}
```

## Example

We will demonstrate how to exclude or include all entity properties using the `AuditMode` method.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

As a result, we will display all created `AuditEntry` and their `AuditEntryItem`.

### Code - AuditMode Method

The `AuditMode` method will exclude or include all entity properties globally for all the bulk operation.
```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer")
    .AuditMode(AuditModeType.ExcludeAll);
        
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list); 

// Result
FiddleHelper.WriteTable("AuditEntry Values", auditEntries.SelectMany(x => x.Values));
FiddleHelper.WriteTable("1 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
FiddleHelper.WriteTable("2 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
```

Try it: [.NET Core](https://dotnetfiddle.net/ezJ9Iu) | [.NET Framework](https://dotnetfiddle.net/BtWOMy)

### Code - AuditMode Property

The `AuditMode` property will set only for any particular bulk operation to include or exclude all entity properties.

```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer");
        
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
    x.AuditMode = AuditModeType.ExcludeAll;
})
.BulkMerge(list); 

// Result
FiddleHelper.WriteTable("AuditEntry Values", auditEntries.SelectMany(x => x.Values));
FiddleHelper.WriteTable("1 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
FiddleHelper.WriteTable("2 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
```

Try it: [.NET Core](https://dotnetfiddle.net/T5MgRa) | [.NET Framework](https://dotnetfiddle.net/ulrLSL)

### Result

We outputted the `AuditEntry` values, but in this case, we have excluded all the properties so there is no result. We have also outputted all the `AuditEntries` list by their `AuditActionType` value:

- `AuditActionType.Insert`: Will display the **1** new customer
- `AuditActionType.Update`: Will display the **2** existing customers
