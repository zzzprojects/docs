---
PermaID: 1000000
Name: EF Extensions
---

# Entity Framework Extensions
## Definition

**Entity Framework Extensions** is a library that dramatically improves EF performances by using bulk and batch operations.

People using this library often report performance enhancement by 50x times and more!

```csharp
// Easy to use
context.BulkSaveChanges();
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => 
	options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## BulkSaveChanges Method

**BulkSaveChanges** method is the upgraded version of **SaveChanges**.

All changes made in the context are persisted in the database but way faster by reducing the number of database round-trip required!

BulkSaveChanges supports everything:

- Association (One to One, One to Many, Many to Many, etc.)
- Complex Type
- Enum
- Inheritance (TPC, TPH, TPT)
- Navigation Property
- Self-Hierarchy
- Etc.

### BulkSaveChanges Examples
```csharp
context.Customers.AddRange(listToAdd); // add
context.Customers.RemoveRange(listToRemove); // remove
listToModify.ForEach(x => x.DateModified = DateTime.Now); // modify

// Easy to use
context.BulkSaveChanges();

// Easy to customize
context.BulkSaveChanges(bulk => bulk.BatchSize = 100);
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkSaveChanges | 90 ms          | 150 ms         | 350 ms         |

## Bulk Operations Methods

Bulk operation methods give you additional flexibility by allowing to customize options such as primary key, columns, include childs entities and more.

They are also faster than **BulkSaveChanges** since they don't use the ChangeTracker and doesn't call the **DetectChanges** method.

Bulk Operations Available:

- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge) (UPSERT operation)

### Bulk Operations Examples
```csharp
// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => 
	options.ColumnPrimaryKeyExpression = customer => customer.Code; });
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert      | 6 ms           | 10 ms          | 15 ms          |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |
| BulkMerge       | 65 ms          | 80 ms          | 110 ms         |

## Requirements

### Entity Framework Version

 - Entity Framework Core 2.x
 - Entity Framework 6.x
 - Entity Framework 5

[Learn more](http://entityframework-extensions.net/)