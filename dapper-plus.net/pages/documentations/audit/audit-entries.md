# AuditEntries

## Description

The `AuditEntries` property which is of type `List<AuditEntry>` gets `UPDATED`, `INSERTED` and `DELETED` data from the database when `UseAudit` is enabled.

## Example

In this example,

We will execute a `BulkMerge` on a list that contains **1** new customers and **2** existing customers.

As a result, we will display all created `AuditEntry`.

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
```

Try it: [.NET Core](https://dotnetfiddle.net/y4w1ZG) | [.NET Framework](https://dotnetfiddle.net/XB5npF)
