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

`options.AuditMode = AuditModeType.ExcludeAll;`

#### To include specific properties

```csharp
options.PostConfiguration = config =>
{
    config.ColumnMappings
        .First(x => x.SourceName == nameof(Customer.CustomerID))
        .AuditMode = ColumnMappingAuditModeType.Include;

    config.ColumnMappings
        .First(x => x.SourceName == nameof(Customer.Name))
        .AuditMode = ColumnMappingAuditModeType.Include;
};
```

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

### Code

```csharp
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>();
context.BulkMerge(list, options =>
{
    options.UseAudit = true;
    options.AuditEntries = auditEntries;
    options.AuditMode = AuditModeType.ExcludeAll;

    options.PostConfiguration = config =>
    {
        config.ColumnMappings
            .First(x => x.SourceName == nameof(Customer.CustomerID))
            .AuditMode = ColumnMappingAuditModeType.Include;

        config.ColumnMappings
            .First(x => x.SourceName == nameof(Customer.Name))
            .AuditMode = ColumnMappingAuditModeType.Include;
    };
});

// Result
FiddleHelper.WriteTable(auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/) | [.NET Framework](https://dotnetfiddle.net/)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.
