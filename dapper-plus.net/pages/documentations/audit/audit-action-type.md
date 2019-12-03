# AuditActionType

## Description

The `AuditActionType` enum represents the action that has been performed (Delete, Insert or Update).

The action is used in the [AuditEntry](audit-entry.md) class.

```csharp
// The namespace is different because the https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>The `AuditActionType` enum represents the action that has been performed (Delete, Insert or Update). The action is used in the [AuditEntry](audit-entry.md) class.</summary>
    public enum AuditActionType
    {
        /// <summary>The name/value that represents the DELETE operation.</summary>
        Delete = 0,

        /// <summary>The name/value that represents the INSERT operation.</summary>
        Insert = 1,

        /// <summary>The name/value that represents the UPDATE operation.</summary>
        Update = 2
    }
}
```

## Example

We will demonstrate how to retrieve `AuditEntries` by the `AuditActionType` value.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

### Code

```csharp
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(options => 
{ 
    options.UseAudit = true;
    options.AuditEntries = auditEntries; 
})
.BulkMerge(list);

// Result
FiddleHelper.WriteTable("1 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
FiddleHelper.WriteTable("2 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
```

Try it: [.NET Core](https://dotnetfiddle.net/7WJZTe) | [.NET Framework](https://dotnetfiddle.net/WTIe5L)

### Result

We outputted `AuditEntries` by the `AuditActionType` value:

- `AuditActionType.Insert`: Will display the **1** new customer
- `AuditActionType.Update`: Will display the **2** existing customers