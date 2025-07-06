---
Name: Getting started with Bulk Operations
---

# Batch Operations

## Definition
Bulk operations methods give you additional flexibility by allowing you to customize options such as primary key, columns, include child entities and more.

They are also faster than BulkSubmitChanges since they don't use the ChangeTracker and don't call the DetectChanges method. 

Bulk Operations Available:
- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge) (UPSERT operation)
- [BulkSynchronize](/bulk-synchronize)

### Bulk Operations Examples
```csharp
// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, bulk => bulk.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## Purpose
Using the ChangeTracker to detect and persist change automatically is great! However, almost every application has some particular scenario which requires some customization and better performance.

For example:
- Inserting hundreds of thousands of data with child entities
- Updating only some particular fields
- Merging a list of customers using the code instead of the key

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SubmitChanges   | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert      | 6 ms           | 10 ms          | 15 ms          |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |
| BulkMerge       | 65 ms          | 80 ms          | 110 ms         |
