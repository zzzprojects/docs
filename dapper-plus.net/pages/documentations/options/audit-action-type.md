# AuditActionType

## Description

The `AuditActionType` is a property of the `AuditEntry` class. It represent the SQL action that has been performed for the entry (A Delete, Insert, or Update).

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
In this example, a BulkMerge will be performed:

- We will retrieve in the first table, all audit entry that has been inserted.
- We will retrieve in the second table, all audit entry that has been updated.

```csharp
// ...code...
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)