# AuditMode

## Description

The `AuditMode` property allows you to exclude or include properties from the auditing.

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

Try it: [.NET Core](https://dotnetfiddle.net/cArNZn) | [.NET Framework](https://dotnetfiddle.net/bd0bzJ)

### Result

We outputted all `AuditEntryItem` auditing metadata. The only information that appears is about the `CustomerID` and `Name` property.
