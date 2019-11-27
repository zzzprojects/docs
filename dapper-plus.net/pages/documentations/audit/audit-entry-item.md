# AuditEntryItem

## Description

The `AuditEntryItem` is a property of the `AuditEntry` class.

It represents metadata about a specific column:
- ColumnName
- NewValue
- OldValue

```csharp
// The https://bulk-operations.net/ library is used under the hood.
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

## Example

We will demonstrate how to see the metadata and changes made to each column in the database.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

As a result, we will display all created `AuditEntry` and their `AuditEntryItem`.

### Code

```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer");

// Connection
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
        
// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list); 

// Result
foreach(var audit in auditEntries.Where(a => a.Action == AuditActionType.Insert))
{
    FiddleHelper.WriteTable(audit.Action.ToString(), audit.Values);
}

foreach(var audit in auditEntries.Where(a => a.Action == AuditActionType.Update))
{
    FiddleHelper.WriteTable(audit.Action.ToString(), audit.Values);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/uMWFra) | [.NET Framework](https://dotnetfiddle.net/IVhoAb)

### Result

We outputted the `AuditEntry` values and we can see the metadata and changes made to each column in the database.

We will split the `AuditEntries` list by their `AuditActionType` value:

- `AuditActionType.Insert`: Will display the **1** new customer
- `AuditActionType.Update`: Will display the **2** existing customers
