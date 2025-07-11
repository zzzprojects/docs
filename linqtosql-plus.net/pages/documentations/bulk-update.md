---
Name: Bulk Update
---

# Bulk Update

## Description

`UPDATE` all entities in the database.

All rows that match the entity key are considered as existing and are `UPDATED` in the database.


```csharp
// Easy to use
context.BulkUpdate(list);

// Easy to customize
context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## Purpose
`Updating` entities using a custom key from file importation is a typical scenario.

Despite the `ChangeTracker` being outstanding to track what's modified, it lacks in terms of scalability and flexibility.

`SubmitChanges` requires one database round-trip for every entity to `update`. So if you need to `update` 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.

`BulkUpdate` on the other hand, offers great customization and requires as few database round-trips as possible.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SubmitChanges   | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |

{% include section-faq-begin.html %}
## FAQ

### How can I specify more than one option?
You can specify more than one option using an anonymous block.


```csharp
context.BulkUpdate(list, options => {
	options.BatchSize = 100;
	options.ColumnInputExpression = c => new {c.ID, c.Name, c.Description};
});
```

### How can I specify the Batch Size?
You can specify a custom batch size using the `BatchSize` option.

Read more: [BatchSize](/batch-size)


```csharp
context.BulkUpdate(list, options => options.BatchSize = 100);
```

### How can I specify custom columns to Update?
You can specify custom columns using the `ColumnInputExpression` option.

Read more: [ColumnInputExpression](/column-input-expression)


```csharp
context.BulkUpdate(list, options => options.ColumnInputExpression = c => new {c.Name, c.Description});
```

### How can I specify custom keys to use?
You can specify a custom key using the `ColumnPrimaryKeyExpression` option.

Read more: [ColumnPrimaryKeyExpression](/column-primary-key-expression)


```csharp
// Single Key
context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);

// Surrogate Key
context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => new { customer.Code1, customer.Code2 });
```

<!--### How can I include child entities (Entity Graph)?
You can include child entities using the `IncludeGraph` option. Make sure to read about the `IncludeGraph` since this option is not as trivial as others.

Read more: [IncludeGraph](/include-graph)!


```csharp
context.BulkUpdate(list, options => options.IncludeGraph = true);
```-->

### Why doesn't BulkUpdate use the ChangeTracker?
To provide the best performance possible!

Since using the `ChangeTracker` can greatly reduce performance, we chose to let `SubmitChanges` method handle scenarios with `ChangeTracker` and `BulkUpdate`, scenarios without it.

### Why BulkUpdate is faster than SubmitChanges?
The major difference between both methods is `SubmitChanges` uses the `ChangeTracker` but not the `BulkUpdate` method.

By skipping the `ChangeTracker`, some methods like `DetectChanges` are no longer required which greatly helps to improve the performance.
{% include section-faq-end.html %}
