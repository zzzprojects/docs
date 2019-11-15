# ColumnMappingAuditModeType

## Description

The `AuditActionType` is a property of the `ColumnMapping` class. 

The default value is `Inherit`.

Inherit: Inherit from the AuditMode from the `BulkOperation` class. When IncludeAll, this column is included; When ExcludeAll, this column is excluded.
Include: The column is always included in the audit.
Exclude: The column is always excluded from the audit.

```csharp
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

> HINT: The `ColumnMappingAuditModeType` is in Z.BulkOperations namespace since the library is used under the hood.

## Example

In this example, 

A BulkMerge will be performed. All columns are included by default but we explicit exclude an image column.


```csharp
// ...code...
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)