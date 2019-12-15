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

Try it: [.NET Core](https://dotnetfiddle.net/IpAPRa) | [.NET Framework](https://dotnetfiddle.net/E5ULln)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.
