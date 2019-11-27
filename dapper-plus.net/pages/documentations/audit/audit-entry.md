# AuditEntry

## Description

The `AuditEntry` represents a row that has been modified.

It represents metadata about a specific row:
- Which tale affected...
- What action has been performed
- When the action has been performed

```csharp
// The https://bulk-operations.net/ library is used under the hood.
namespace Z.BulkOperations
{
    /// <summary>An audit entry.</summary>
    public class AuditEntry
    {
        /// <summary>Gets or sets the action.</summary>
        /// <value>The action.</value>
        public AuditActionType Action { get; set; }
        
        /// <summary>Gets or sets the Date/Time of the date.</summary>
        /// <value>The date.</value>
        public DateTime Date { get; set; }

        /// <summary>Gets or sets the metas.</summary>
        /// <value>The metas.</value>
        public Dictionary<object, object> Metas { get; set; }

        /// <summary>Gets or sets the name of the table.</summary>
        /// <value>The name of the table.</value>
        public string TableName { get; set; }

        /// <summary>Gets or sets the values.</summary>
        /// <value>The values.</value>
        public List<AuditEntryItem> Values { get; set; }
    }
}
```

## Example

We will demonstrate how to get information about the modified row.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

As a result, we will display all created `AuditEntry` and their `AuditEntryItem`.

### Code

```csharp
// Mapping
DapperPlusManager.Entity<Customer>().Table("Customer");

// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>(); 
connection.UseBulkOptions(x => 
{ 
    x.AuditEntries = auditEntries; 
    x.UseAudit = true;
})
.BulkMerge(list);

// Result
FiddleHelper.WriteTable("1 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
FiddleHelper.WriteTable("2 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
```

Try it: [.NET Core](https://dotnetfiddle.net/Zz2EcH) | [.NET Framework](https://dotnetfiddle.net/UJg7Cr)

### Result

We outputted `AuditEntries` list grouped by their `AuditActionType` value:

- `AuditActionType.Insert`: Will display the **1** new customer
- `AuditActionType.Update`: Will display the **2** existing customers
