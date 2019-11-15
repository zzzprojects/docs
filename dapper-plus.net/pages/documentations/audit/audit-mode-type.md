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

We will demonstrate how the `Action` property is populated.

### Execute
A `BulkMerge` will be executed on a list that contains **1** new customer and **2** existing customers.

With the following **BulkOptions**:

- `UseAudit`: To enable the audit feature
- `AuditEntries`: To retrieve audit entries

```csharp
// ...code...

// Try it online to see the result
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)

### Result

The `AuditEntries` property will contains **3** `AuditEntry` with the `Action` property value set to:

- `AuditActionType.Insert`: For the **1** new customer
- `AuditActionType.Update`: For the **2** existing customers

## Example
In this example,

We will demonstrate how to include only specific properties.

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

With the following **Mapping**:

- `AuditMode(AuditModeType.ExcludeAll)`: To exclude all properties from the auditing
- `AuditMode(AuditModeType.Include, x => new { x.Code, x.Description })`: To include the `Code` and `Description` properties in the auditing.

With the following **BulkOptions**:

- `UseAudit`: To enable the audit feature
- `AuditEntries`: To retrieve audit entries

As a result,

The **AuditEntries** property will contains **3** `AuditEntry` with the `Action` property value set to:

ThOnly the `Code` and `Description` properties will be populated in the `AuditEntry` list.

```csharp
// ...code...
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)
