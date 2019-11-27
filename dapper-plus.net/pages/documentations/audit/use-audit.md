# UseAudit

## Description

The `UseAudit` property sets if `UPDATED`, `INSERTED` and `DELETED` data from the database should be returned as `AuditEntries`.

## Example

We will demonstrate how `UseAudit` property sets `UPDATED`, `INSERTED` and `DELETED` data from the database.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer, and **2** existing customers.

As a result, we will display all the `AuditEntry`.

### Code

The following example sets `UseAudit` to `true` and assigns the list of `AuditEntries` to populate.

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
FiddleHelper.WriteTable("1 - Inserted Customers", auditEntries.Where(x => x.Action == AuditActionType.Insert));
FiddleHelper.WriteTable("2 - Updated Customers", auditEntries.Where(x => x.Action == AuditActionType.Update));
```

Try it: [.NET Core](https://dotnetfiddle.net/cjasQV) | [.NET Framework](https://dotnetfiddle.net/s8wLF9)

### Result

We outputted `AuditEntries` list grouped by their `AuditActionType` value:

- `AuditActionType.Insert`: Will display the **1** new customer
- `AuditActionType.Update`: Will display the **2** existing customers
