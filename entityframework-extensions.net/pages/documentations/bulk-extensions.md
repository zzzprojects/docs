---
Title: EFCore BulkExtensions | Bulk Insert, Update, Delete, Merge, Upsert
MetaDescription: Learn how to use EFCore BulkExtensions to boost your CRUD operations performance. Discover how to save entities faster and by using less memory. Try it now.
LastMod: 2024-08-30
---

# EFCore BulkExtensions

## What is EFCore BulkExtensions?

**BulkExtensions** are extension methods added to **EFCore** to enhance your CRUD operation performance and reduce the memory usage in your application. Bulk Methods has been created to be very easy to use and easy to customize.

Here is an example that [Bulk Insert in EF Core](https://entityframework-extensions.net/bulk-insert) a list of `Invoice` and related `InvoiceItems` by using the [IncludeGraph](https://entityframework-extensions.net/include-graph) option:

```csharp
context.BulkInsert(invoices, options => { options.IncludeGraph = true});
```

The [Bulk Insert](https://entityframework-extensions.net/bulk-insert) method is not only faster than using the `SaveChanges` method but also [reduces the memory consumption](https://entityframework-extensions.net/v7-100-0-0-include-graph#memory-performance-improvements)


**10k invoices, 50k InvoiceItem**:

| | IncludeGraph  | SaveChanges |
| -- | :--: | :--: |
| Memory | 60 MB | 200 MB |
| Performance | 1.5s | 6s |

**100k invoices, 500k InvoiceItem**:

| | IncludeGraph | SaveChanges |
| -- | :--: | :--: |
| Memory | 400 MB | 1800 MB |
| Performance | 10s | 58s |

Using **BulkExtensions** can improve your performance by more than **5 times faster** and with the benefit of using around **20% of the memory** of what **SaveChanges** requires.

## Which EFCore BulkExtensions method are supported?

- [Bulk SaveChanges](/bulk-savechanges)
- [Bulk Insert](/bulk-insert)
- [Bulk Update](/bulk-update)
- [Bulk Delete](/bulk-delete)
- [Bulk Merge](/bulk-merge)
- [Bulk Synchronize](/bulk-synchronize)
- [Bulk Read](/bulk-read)
- [Where Bulk Contains](/where-bulk-contains)
- [Where Bulk Not Contains](/where-bulk-not-contains)

### Which version of Entity Framework is supported?
- **Entity Framework Core Version**: EF Core 8, EF Core 7, EF Core 6, EF Core 5, and lower
- **Entity Framework Version**: EF6 and lower

### Which provider is supported for Bulk Extensions?

All major providers are supported:
- SQL Server
- MySQL
- MariaDB
- Oracle
- PostgreSQL
- SQLite


## Bulk Insert

The [EF Core Bulk Insert](/bulk-insert) extension method is the main method of our **BulkExtensions** library. It allows you to insert thousands and millions of entities in your database quickly and efficiently.

The most common options used are:

- **AutoMapOutputDirection**: This option allows to optimize performance by not returning outputting values such as identity values.
- **InsertIfNotExists**: This option ensures only new entities that don't already exist in the database are inserted.
- **InsertKeepIdentity**: This option allows the insertion of specific values into an identity column from your entities.
- **IncludeGraph**: This option enables the insertion of entities along with all related entities found in the entity graph, maintaining the relationships.

```csharp
context.BulkInsert(customers);
context.BulkInsert(customers, options => {
	options.AutoMapOutputDirection = false;
	options.InsertIfNotExists = true;
});
context.BulkInsert<Customer>(anonymousList);

await context.BulkInsertAsync(customers);
```

## Bulk SaveChanges

The [EF Core Bulk SaveChanges](/bulk-savechanges) extension method allows you to replace the `SaveChanges` method to improve your saving performance. The only difference between both methods is the `BulkSaveChange` inserts, updates, and deletes data faster!

```csharp
// context.SaveChanges();
context.BulkSaveChanges();
context.BulkSaveChanges(options => { });

await context.BulkSaveChangesAsync();
```

## Bulk Update

The [EF Core Bulk Update](/bulk-update) extension method allows you to update data in your database in bulk.

The most common options used are:

- **ColumnPrimaryKeyExpression**: This option allows you to use a custom key to check for pre-existing entities.
- **ColumnInputExpression**: This option enables you to specify a subset of columns to update by using an expression.
- **ColumnInputNames**: This option allows you to specify a subset of columns to update by providing their names.
- **IncludeGraph**: This option allows updating entities along with all related entities found in the entity graph, maintaining the data relationships.

```csharp
context.BulkUpdate(customers);
context.BulkUpdate(customers, options => {
	options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
});
context.BulkUpdate<Customer>(anonymousList);

await context.BulkUpdateAsync(customers);
```

## Bulk Delete

The [EF Core Bulk Delete](/bulk-delete) extension method allows you to delete data in your database in bulk.

The most common options used are:

- **ColumnPrimaryKeyExpression**: This option allows the usage of a custom key to verify the existence of entities.
- **DeleteMatchedAndConditionExpression**: This option enables you to perform or skip the deletion action based on whether all values from the source and destination are equal for the specified properties.
- **DeleteMatchedAndOneNotConditionExpression**: This option allows you to perform or skip the deletion action if at least one value from the source differs from the destination for the specified properties.
- **DeleteMatchedAndFormula**: This option lets you perform or skip the deletion action based on a predefined SQL condition.

```csharp
context.BulkDelete(customers);
context.BulkDelete(customers, options => {
	options.DeleteMatchedAndFormula = "DestinationTable.CreatedDate < DATEADD(YEAR, -2, GETDATE())";
});
context.BulkDelete<Customer>(anonymousList);

await context.BulkDeleteAsync(customers);
```

## Bulk Merge / Upsert

The [EF Core Bulk Merge](/bulk-merge) extension method allows you to add or update data in your database in bulk. People often search for this feature on other names, such as `Upsert`, `AddOrUpdate`, or `InsertOrUpdate` bulk extensions.

The most common options used are:

- **ColumnPrimaryKeyExpression**: This option allows for the use of a custom key to verify the existence of entities.
- **IgnoreOnMergeInsertExpression**: This option enables you to ignore certain columns only for the insert phase of the merge operation.
- **IgnoreOnMergeUpdateExpression**: This option allows you to ignore specific columns only during the update phase of the merge operation.
- **IncludeGraph**: This option ensures updating of entities along with all related entities found in the entity graph, preserving data relationships.

```csharp
context.BulkMerge(customers);
context.BulkMerge(customers, options => {
	options.ColumnPrimaryKeyExpression = x => new { x.Code };
	options.IgnoreOnMergeInsertExpression = x => new { x.UpdatedDate, x.UpdatedBy };
	options.IgnoreOnMergeUpdateExpression = x => new { x.CreatedDate, x.CreatedBy };
});
context.BulkMerge<Customer>(anonymousList);

await context.BulkMergeAsync(customers);
```

## Bulk Synchronize

The [EF Core Bulk Synchronize](/bulk-synchronize) extension method allows you to insert or update or delete data in your database in bulk. In other words, the data of your database becomes a mirror of the entities you provided. People often search for this feature as `AddOrUpdateOrDelete` or `InsertOrUpdateOrDelete` bulk extensions.

```csharp
context.BulkSynchronize(customers);
context.BulkSynchronize(customers, options => { });
context.BulkSynchronize<Customer>(anonymousList);

await context.BulkSynchronizeAsync(customers);
```

## Bulk Read

The [EF Core BulkRead](/bulk-read) extension method allows you to retrieve entities from your database by:

- providing a list of `ids` (int, guid, string, etc.)
- providing a list of entities and using the model key or providing a custom one

```csharp
var list1 = context.Customers.BulkRead(ids);
var list2 = context.Customers.BulkRead(list, key => new { x.Email, x.PhoneNumber });

var list3 = await context.Customers.BulkReadAsync(ids);
```

## Where Bulk Contains

The [EF Core WhereBulkContains](/where-bulk-contains) extension method allows you to retrieve entities from your database by:

It can be used with practically any type of data source, such as:

- Basic types like `List<int>` and `List<Guid>`
- Entity Types like `List<Customer>`
- Anonymous Types
- Lists of Expando Objects

The `WhereBulkContains` is a deferred method (so it doesn't materialize entities), while the `BulkRead` is an immediate method (under the hood, it calls the `WhereBulkContains` + `ToList()`).

```csharp
var list1 = context.Customers.WhereBulkContains(ids).ToList();
var list2 = context.Customers.WhereBulkContains(list, key => new { x.Email, x.PhoneNumber });

var list3 = await context.Customers.WhereBulkContains(ids).ToListAsync();
```

## Where Bulk Not Contains

The [EF Core WhereBulkNotContains](/where-bulk-not-contains) method is similar to the `WhereBulkContains` method, but this time, it returns all data from the database that cannot be retrieved from the provided list.

The `WhereBulkNotContains` is a deferred method (so it doesn't materialize entities), while the `BulkRead` is an immediate method (under the hood, it calls the `WhereBulkContains` + `ToList()`).

```csharp
var list1 = context.Customers.WhereBulkContains(ids).ToList();
var list2 = context.Customers.WhereBulkContains(list, key => new { x.Email, x.PhoneNumber });

var list2 = await context.Customers.WhereBulkContains(ids).ToListAsync();
```

## Conclusion

Learning how to use `EFCore BulkExtensions` to improve critical parts of your application that could benefit from performance enhancement or memory usage reduction is an essential tool that any developer must know. In addition to performance and memory benefits, you also have access to hundreds of options to customize your bulk operations and save the data the way you desire.