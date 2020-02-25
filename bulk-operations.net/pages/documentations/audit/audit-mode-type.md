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

#### To exclude all properties

`bulk.AuditMode = AuditModeType.ExcludeAll;`

#### To include specific properties

```csharp
bulk.ColumnMappings.Add(
    new ColumnMapping<Customer>("CustomerID", true)
    {
        AuditMode = ColumnMappingAuditModeType.Include
    }
);
bulk.ColumnMappings.Add(
    new ColumnMapping<Customer>("Name")
    {
        AuditMode = ColumnMappingAuditModeType.Include
    }
);
```

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

### Code

```csharp
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
using (var bulk = new BulkOperation(connection))
{
    bulk.DestinationTableName = "Customer";
    bulk.UseAudit = true;

    bulk.AuditMode = AuditModeType.ExcludeAll;
    
    bulk.ColumnMappings.Add(
        new ColumnMapping<Customer>("CustomerID", true)
        {
            AuditMode = ColumnMappingAuditModeType.Include
        }
    );
    bulk.ColumnMappings.Add(
        new ColumnMapping<Customer>("Name")
        {
            AuditMode = ColumnMappingAuditModeType.Include
        }
    );
    bulk.BulkMerge(list);

    auditEntries = bulk.AuditEntries;
}

// Result
FiddleHelper.WriteTable(auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/jF9GA7) | [.NET Framework](https://dotnetfiddle.net/m6B7Xr)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.
