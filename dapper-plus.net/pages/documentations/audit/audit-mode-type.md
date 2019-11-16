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

A BulkMerge will be performed. All columns will be excluded but we will include only the primary key.


```csharp
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
        
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
    x.AuditMode = AuditModeType.ExcludeAll;
})
.BulkMerge(list); 
```

Try it: [.NET Framework](https://dotnetfiddle.net/ObXmob) | [.NET Core](https://dotnetfiddle.net/wnbHSR)
