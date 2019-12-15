# AuditMode

## Description

The `AuditMode` property allows you to exclude or include properties from the auditing.

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

Try it: [.NET Core](https://dotnetfiddle.net/cArNZn) | [.NET Framework](https://dotnetfiddle.net/bd0bzJ)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.
