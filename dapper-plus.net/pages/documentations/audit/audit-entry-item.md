# AuditEntryItem

## Description

The `AuditEntryItem` is a property of the `AuditEntry` class.

It represents metadata about a specific column:
- ColumnName
- NewValue
- OldValue

```csharp
namespace Z.BulkOperations
{
    /// <summary>An audit entry item.</summary>
    public class AuditEntryItem
    {
        /// <summary>Gets or sets the name of the column.</summary>
        /// <value>The name of the column.</value>
        public string ColumnName { get; set; }

        /// <summary>Gets or sets the new value.</summary>
        /// <value>The new value.</value>
        public object NewValue { get; set; }

        /// <summary>Gets or sets the old value.</summary>
        /// <value>The old value.</value>
        public object OldValue { get; set; }
    }
}
```

> HINT: The `AuditEntryItem` is in Z.BulkOperations namespace since the library is used under the hood.

## Example

In this example,

We will execute a `BulkMerge` on a list that contains **1** new customers and **2** existing customers.

As a result, we will display all created `AuditEntry` and their `AuditEntryItem`.


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

Try it: [.NET Framework](https://dotnetfiddle.net/IVhoAb) | [.NET Core](https://dotnetfiddle.net/uMWFra)
