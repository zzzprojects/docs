# AuditActionType

## Description

The `AuditActionType` is a property of the `AuditEntry` class. It represents the SQL action that has been performed for the entry (A Delete, Insert, or Update).

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
List<AuditEntry> auditEntries = new List<AuditEntry>();
        
List<Customer> customers = connection.Query<Customer>("SELECT * FROM Customers").ToList();

customers.ForEach(c => 
{
    c.Name += "_Updated";
    c.Description += "_Updated";
});

customers.Add(new Customer() { Name = "Customer_4", Description = "Description for Customer 4", IsActive = true });
customers.Add(new Customer() { Name = "Customer_4", Description = "Description for Customer 4", IsActive = true });

connection.UseBulkOptions(x =>
{
    x.AuditEntries = auditEntries;
    x.UseAudit = true;
})
.BulkMerge(customers);
        
foreach(var audit in auditEntries.Where(a => a.Action == AuditActionType.Update))
{
    FiddleHelper.WriteTable(audit.Action.ToString(), audit.Values);
}
        
foreach(var audit in auditEntries.Where(a => a.Action == AuditActionType.Insert))
{
    FiddleHelper.WriteTable(audit.Action.ToString(), audit.Values);
}
```

Try it: [.NET Framework](https://dotnetfiddle.net/ASctoz) | [.NET Core](https://dotnetfiddle.net/T1pyjd)
