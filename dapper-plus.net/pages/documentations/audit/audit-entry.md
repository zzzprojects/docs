# AuditEntry

## Description

The `AuditEntry` represents a row that has been modified.

It represents metadata about a specific row:
- Which tale affected...
- What action has been performance
- When the action has been performed

```csharp
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

> HINT: The `AuditEntry` is in Z.BulkOperations namespace since the library is used under the hood.

## Example

In this example,

We will execute a `BulkMerge` on a list that contains **1** new customers and **2** existing customers.

As a result, we will display all created `AuditEntry`.

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
```

Try it: [.NET Framework](https://dotnetfiddle.net/UJg7Cr) | [.NET Core](https://dotnetfiddle.net/Zz2EcH)
