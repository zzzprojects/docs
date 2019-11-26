# AuditEntries

## Description

The `AuditEntries` property which is of type `List<AuditEntry>` gets `UPDATED`, `INSERTED` and `DELETED` data from the database when `UseAudit` is enabled.

## Example

We will demonstrate how `AuditEntries` gets `UPDATED`, `INSERTED` and `DELETED`.

## Execute

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

As a result, we will display all the `AuditEntry` values.

## Code

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
FiddleHelper.WriteTable(auditEntries.SelectMany(x => x.Values));
```

Try it: [.NET Core](https://dotnetfiddle.net/y4w1ZG) | [.NET Framework](https://dotnetfiddle.net/XB5npF)

## Result

We outputted the `AuditEntry` values which displays the `ColumnName`, `NewValue` and `OldValue`.
