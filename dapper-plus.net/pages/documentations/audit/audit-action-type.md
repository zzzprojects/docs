# AuditActionType

## Description

The `AuditActionType` is a property of the `AuditEntry` class.

It represent the SQL action that has been performed for the entry:
- Delete
- Insert
- Update

```csharp
namespace Z.BulkOperations
{
    /// <summary>Values that represent AuditActionType.</summary>
    public enum AuditActionType
    {
        /// <summary>An enum constant representing the delete option.</summary>
        Delete,

        /// <summary>An enum constant representing the insert option.</summary>
        Insert,

        /// <summary>An enum constant representing the update option.</summary>
        Update
    }
}
```

> HINT: The `AuditActionType` is in Z.BulkOperations namespace since the library is used under the hood.

## Example

In this example,

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

// will display in a grid...
As a result, **3** `AuditEntry` will be created with the `Action` property value set to:
- `AuditActionType.Insert` for the **1** new customer
- `AuditActionType.Update` for the **2** existing customers

```csharp
// ...code...

// Try it online to see the result
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)
