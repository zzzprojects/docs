---
Name: How do you filter a list to return only items from the list that match a row in the database?
LastMod: 2024-01-16
---

# How do you filter a list to return only items from the list that match a row in the database?

## Description

The `WhereBulkContainsFilterList` method lets you filter a list by only returning items from the list that already exists in the database.

### Example

```csharp
// return all customers from the 'deserializedCustomers' that exists in the database
var existingCustomers = context.Customers.WhereBulkContainsFilterList(deserializedCustomers).ToList();

// from the existing list, you can retrieve all non-existing customers by excluding existing ones without fetching the database this time.
var notExistingCustomers = deserializedCustomers.Exclude(existingCustomer);
```

## Scenario

Filtering a list to know which items already exist or not can sometimes be very useful.

For example, let's say that you want to split the `insert` and `update` logic as they have very different application rules. Instead of using the [Bulk Merge](/bulk-merge) method, you can now split the list with existing and non-existing items and use the [Bulk Insert](/bulk-insert) and [Bulk Update](/bulk-update) methods from both lists.

```csharp
// return all customers from the 'deserializedCustomers' that exists in the database
var existingCustomers = context.Customers.WhereBulkContainsFilterList(deserializedCustomers).ToList();

// from the existing list, you can retrieve all non-existing customers by excluding them without fetching the database this time.
var notExistingCustomers = deserializedCustomers.Exclude(existingCustomer);

context.BulkInsert(notExistingCustomers);
context.BulkUpdate(existingCustomers);
```

## FAQ

- [How to use the method WhereBulkContainsFilterList?](#how-to-use-the-method-wherebulkcontainsfilterlist)
- [What is the difference between the method WhereBulkContainsFilterList and WhereBulkContains?](#what-is-the-difference-between-the-method-wherebulkcontainsfilterlist-and-wherebulkcontains)
- [Where can I learn more about the method WhereBulkContainsFilterList?](#where-can-i-learn-more-about-the-method-wherebulkcontainsfilterlist)

## How to use the method WhereBulkContainsFilterList?

The most basic scenario is passing a list to the `WhereBulkContainsFilterList` method.

The `WhereBulkContainsFilterList` method will filter the list to return only item that exists in the database.

```csharp
// The `JOIN` statement will use the default entity key if none is provided (CustomerID)
var customers = context.Customers.WhereBulkContainsFilterList(deserializedCustomers);

// You can specify a custom `JOIN` clause with one or many properties using a `Lambda Expression`
var customers = context.Customers.WhereBulkContainsFilterList(deserializedCustomers, x => x.Code);

// You can specify a custom `JOIN` clause with one or many properties using a `List<string>`
var customers = context.Customers.WhereBulkContainsFilterList(deserializedCustomers, new List<string> { "Code" });

// You can specify a custom `JOIN` clause with one or many properties using a `params string[]`
var customers = context.Customers.WhereBulkContainsFilterList(deserializedCustomers, "Code");
```

[Try it](https://dotnetfiddle.net/Dg2FUC)

The `WhereBulkContainsFilterListAsync` method is also supported.

## What is the difference between the method WhereBulkContainsFilterList and WhereBulkContains?

The `WhereBulkContainsFilterList` method is similar to the `WhereBulkContains` method, but what is returned is very different.

- The `WhereBulkContainsFilterList` method returns items from the list that match a row in the database.
- The `WhereBulkContains` method returns entities from the database that match an item in the list

So, the method you use depends on whether you want to filter the current list or instead return entities from the database.

## Where can I learn more about the method WhereBulkContainsFilterList?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.

## Related Solutions

- [WhereBulkNotContainsFilterList](/where-bulk-not-contains-filter-list)
- [WhereBulkContains](/where-bulk-contains)
- [WhereBulkNotContains](/where-bulk-not-contains)
- [BulkRead](/bulk-read)