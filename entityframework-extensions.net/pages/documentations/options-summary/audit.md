---
Title: Auditing in Entity Framework Extensions  
MetaDescription: Learn how to use auditing options in Entity Framework Extensions for EF Core and EF6. Discover how to use `UseAudit`, `AuditEntries`, and `AuditEntry` to capture which data has been inserted, modified, or deleted in your database.
LastMod: 2025-10-30
---

# 📜 Auditing in Entity Framework Extensions/n Capture data that has been inserted, modified, or deleted in your database

The **audit feature** in Entity Framework Extensions lets you **track all changes** made to your database during any **bulk operation**.

Once captured, you can **log the old and new values** directly in your database, write them to a log file, or simply use the audit results within your application.

The **audit feature** works with all bulk operations:

* [BulkInsert](/bulk-insert)
* [BulkInsertOptimized](/bulk-insert-optimized)
* [BulkUpdate](/bulk-update)
* [BulkDelete](/bulk-delete)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)
* [BulkSaveChanges](/bulk-savechanges)

## Key Features

* Keep a complete history of all modifications
* [Store audit data in a database](/save-audit-history-in-a-database) or a log file
* Track **who**, **what**, and **when** a modification occurred
* Compare **old values** and **new values** for every change

## Getting Started

To use the audit feature, you need to enable it and provide a list of the AuditEntry that will be populated during the operations.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.BulkOperations;
using Z.EntityFramework.Extensions;

// Execute
List<AuditEntry> auditEntries = new List<AuditEntry>();
context.BulkMerge(list, options =>
{
    options.UseAudit = true;
    options.AuditEntries = auditEntries;
});
```

## Class Definition

The audit feature includes **three main classes**:

* **AuditEntry**: Represents a row and contains information about the operation performed.
* **AuditEntryItem**: Represents a column within a row and contains the old and new values.
* **AuditActionType**: Defines the type of action performed (Insert, Update, Delete, etc.).

```csharp
public class AuditEntry
{
	public AuditActionType Action { get; set; }
	public DateTime Date { get; set; }
	public Dictionary<object, object> Metas { get; set; } // Hold any custom information you want to add
	public string TableName { get; set; }
	public List<AuditEntryItem> Values { get; set; }
}
```

```csharp
public class AuditEntryItem
{
	public string ColumnName { get; set; }
	public object NewValue { get; set; }
	public object OldValue { get; set; }
}
```

```csharp
public enum AuditActionType
{
	Delete,
	Insert,
	Update,
	SoftDelete,
}
```

## FAQ

### Why does enabling this option decrease performance?

When this option is enabled, **additional SQL statements** are required to return all old and new values.

For some providers, such as **SQL Server**, it can no longer use `SqlBulkCopy` directly on the destination table when a column needs to be outputted. Instead, it must create a **temporary table**, which adds some extra overhead and reduces performance.

The impact can vary:

* It is **major** when the bulk strategy needs to be changed (for example, when `SqlBulkCopy` can’t be used).
* It is **minor** when data was already being output, as the additional cost is limited.

## Limitations

* **DOES NOT** support Oracle.

## Conclusion

The **audit feature** in Entity Framework Extensions is a powerful tool when you need to **keep track of what happens during your bulk operations**.

With just a few lines of code, you can capture **who changed what and when**, store this information anywhere you want, and even compare old and new values for full transparency.

While enabling auditing can slightly impact performance, it gives you complete visibility and control over your data changes — something that’s often worth the trade-off in real-world applications.

If you care about **data integrity, traceability, and accountability**, enabling auditing is one of the easiest ways to achieve it in your EF Core or EF6 projects.