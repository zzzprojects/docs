# AuditModeType

## Description

The `AuditActionType` is a property of the `BulkOperation` class. 

The default value is `IncludeAll`.

IncludeAll: All columns are audited by default. To exclude a column, you need to specify it at the column mapping level
ExcludeAll: All columns are NOT audited by default. To include a column, you need to specify it at the column mapping level

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

> HINT: The `AuditModeType` is in Z.BulkOperations namespace since the library is used under the hood.

## Example
In this example, 

A BulkMerge will be performed. All column will be excluded but we will include only the primary key.


```csharp
// ...code...
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)