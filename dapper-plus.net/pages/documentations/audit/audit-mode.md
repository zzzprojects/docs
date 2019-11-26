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

In this example,

We will execute a `BulkMerge` on a list that contains **1** new customers and **2** existing customers.



As a result, we will display all created `AuditEntry` and their `AuditEntryItem`.



In this example,

The following example sets `UseAudit` to `true`, assigns the list of `AuditEntries` to populate, and also sets the `AuditMode` to `AuditModeType.ExcludeAll`.

```csharp
namespace Z.BulkOperations
{
    /// <summary>Values that represent AuditModeType.</summary>
    public enum AuditModeType
    {
        IncludeAll,
        ExcludeAll
    }
}
```

Try it: [.NET Core](https://dotnetfiddle.net/T5MgRa) | [.NET Framework](https://dotnetfiddle.net/ulrLSL)

## Example

The `AuditMode` property can also be set globally instead of only for any particular bulk operation to include or exclude all columns from the database.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers")
    .Identity(x => x.CustomerID)
    .AuditMode(AuditModeType.ExcludeAll);
        
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
        
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list);
```

Try it: [.NET Core](https://dotnetfiddle.net/ezJ9Iu) | [.NET Framework](https://dotnetfiddle.net/BtWOMy)
