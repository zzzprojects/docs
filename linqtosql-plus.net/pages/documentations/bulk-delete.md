---
Name: Bulk Delete
---

# Bulk Delete

## Description

`DELETE` all entities from the database.

All rows that match the entity key are `DELETED` from the database.


```csharp
// Easy to use
context.BulkDelete(list);

// Easy to customize
context.BulkDelete(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## Purpose
`Deleting` entities using a custom key from file importation is a typical scenario.

Despite the `ChangeTracker` being outstanding to track what's modified, it lacks in terms of scalability and flexibility.

`SubmitChanges` requires one database round-trip for every entity to `delete`. So if you need to `delete` 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.

`BulkDelete` in counterpart offers great customization and requires the fewest database round-trips as possible.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SubmitChanges   | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |

{% include section-faq-begin.html %}
## FAQ

### How can I specify more than one option?
You can specify more than one option using an anonymous block.


```csharp
context.BulkDelete(list, options => {
	options.BatchSize = 100;
	options.ColumnInputExpression = c => new {c.ID, c.Name, c.Description};
});
```

### How can I specify the Batch Size?
You can specify a custom batch size using the `BatchSize` option.

Read more: [BatchSize](/batch-size)


```csharp
context.BulkDelete(list, options => options.BatchSize = 100);
```

### How can I specify custom keys to use?
You can specify a custom key using the `ColumnPrimaryKeyExpression` option.

Read more: [ColumnPrimaryKeyExpression](/column-primary-key-expression)


```csharp
// Single Key
context.BulkDelete(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);

// Surrogate Key
context.BulkDelete(customers, options => options.ColumnPrimaryKeyExpression = customer => new { customer.Code1, customer.Code2 });
```

<!--### How can I include child entities (Entity Graph)?
You cannot. Due to the risk of mistakes, we preferred not to offer this option and make sure every entity you wish to `delete` is specified.!-->

### Why doesn't BulkDelete use the ChangeTracker?
To provide the best performance possible!

Since using the `ChangeTracker` can greatly reduce performance, we chose to let `SubmitChanges` method handle scenarios with `ChangeTracker` and `BulkDelete`, scenarios without it.

### Why BulkDelete is faster than SubmitChanges?
The major difference between both methods is `SubmitChanges` uses the `ChangeTracker` but not the `BulkDelete` method.

By skipping the `ChangeTracker`, some methods like `DetectChanges` are no longer required which greatly helps to improve the performance.
{% include section-faq-end.html %}
