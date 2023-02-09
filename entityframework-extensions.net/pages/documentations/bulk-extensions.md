---
Name: Bulk Extensions
---

## Entity Framework Bulk Extensions

## What is Bulk Extensions in Entity Framework?

Bulk Extensions are methods created to extend EF Core and EF6 to enhance CRUD performance. They allow you to improve performance and easily customizing the way you are saving your data. For example, you can use the `BulkInsert` method instead of `SaveChanges` method to insert only specifics columns or to insert entities that doesn't already exists.

Here is a list of **EF6** and **EF Core Bulk Extensions** methods:

- [Bulk SaveChanges](#bulk-savechanges)
- [Bulk Insert](#bulk-insert)
- [Bulk Update](#bulk-update)
- [Bulk Delete](#bulk-delete)
- [Bulk Merge](#bulk-merge)
- [Bulk Synchronize](#bulk-synchronize)
- [Bulk Read](#bulk-read)
- [Where Bulk Contains](#where-bulk-contains)
- [Where Bulk Not Contains](#where-bulk-not-contains)

## Bulk SaveChanges

The `BulkSaveChanges` extension method allows you to replace the `SaveChanges` method to improve your saving performance. The only difference between both methods is the `BulkSaveChange` inserts, updates, and deletes data faster!

```csharp
// context.SaveChanges();
context.BulkSaveChanges();
context.BulkSaveChanges(options => { });

await context.BulkSaveChangesAsync();
```

See the [SaveChanges](/bulk-savechanges) documentation for more information and examples.

## Bulk Insert

The `BulkInsert` extension method allows you to insert thousands or millions of data into your database. This method is the fastest way to insert in Bulk in EF Core and EF6.

```csharp
context.BulkInsert(customers);
context.BulkInsert(customers, options => { });
context.BulkInsert<Customer>(anonymousList);

await context.BulkInsertAsync(customers);
```

See the [Bulk Insert](/bulk-insert) documentation for more information and examples.

## Bulk Update

The `BulkUpdate` extension method allows you to update data in your database in bulk.

```csharp
context.BulkUpdate(customers);
context.BulkUpdate(customers, options => { });
context.BulkUpdate<Customer>(anonymousList);

await context.BulkUpdateAsync(customers);
```

See the [Bulk Update](/bulk-update) documentation for more information and examples.

## Bulk Delete

The `BulkDelete` extension method allows you to update data in your database in bulk.

```csharp
context.BulkDelete(customers);
context.BulkDelete(customers, options => { });
context.BulkDelete<Customer>(anonymousList);

await context.BulkDeleteAsync(customers);
```

See the [Bulk Delete](/bulk-delete) documentation for more information and examples.

## Bulk Merge

The `BulkMerge` extension method allows you to insert or update data in your database in bulk. People often search for this feature on other names, such as `Upsert`, `AddOrUpdate`, or `InsertOrUpdate` bulk extensions.

```csharp
context.BulkMerge(customers);
context.BulkMerge(customers, options => { });
context.BulkMerge<Customer>(anonymousList);

await context.BulkMergeAsync(customers);
```

See the [Bulk Merge](/bulk-merge) documentation for more information and examples.

## Bulk Synchronize

The `BulkSynchronize` extension method allows you to insert or update or delete data in your database in bulk. In other words, the data of your database become a mirror of the entities you provided. People often search for this feature as `AddOrUpdateOrDelete` or `InsertOrUpdateOrDelete` bulk extensions.

```csharp
context.BulkSynchronize(customers);
context.BulkSynchronize(customers, options => { });
context.BulkSynchronize<Customer>(anonymousList);

await context.BulkSynchronizeAsync(customers);
```

See the [Bulk Synchronize](/bulk-synchronize) documentation for more information and examples.

## Bulk Read

The `BulkRead` extension method allows you to retrieve entities from your database by:

- providing a list of `ids` (int, guid, string, etc.)
- providing a list of entities and using the model key or providing a custom one

```csharp
context.Customers.BulkRead(ids);
context.Customers.BulkRead(list, key => new { x.Email, x.PhoneNumber });

await context.Customers.BulkReadAsync(ids);
```

See the [Bulk Read](/bulk-read) documentation for more information and examples.

## Where Bulk Contains

The `WhereBulkContains` extension method allows you to retrieve entities from your database by:

- providing a list of `ids` (int, guid, string, etc.)
- providing a list of entities and using the model key or providing a custom one

The `WhereBulkContains` is a deferred method (so it doesn't materialize entities), while the `BulkRead` is an immediate method (under the hood, it calls the `WhereBulkContains` + `ToList()`).

```csharp
context.Customers.WhereBulkContains(ids).ToList();
context.Customers.WhereBulkContains(list, key => new { x.Email, x.PhoneNumber });

await context.Customers.WhereBulkContains(ids).ToListAsync();
```

See the [Where Bulk Contains](/where-bulk-contains) documentation for more information and examples.

## Where Bulk Not Contains

The `WhereBulkNotContains` method is similar to the `WhereBulkContains` method, but this time, it returns all data from the database that cannot be retrieved from the provided list.

```csharp
context.Customers.WhereBulkContains(ids).ToList();
context.Customers.WhereBulkContains(list, key => new { x.Email, x.PhoneNumber });

await context.Customers.WhereBulkContains(ids).ToListAsync();
```

See the [Where Bulk Not Contains](/where-bulk-not-contains) documentation for more information and examples.