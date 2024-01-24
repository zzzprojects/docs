---
Name: How do you filter a list to return only items from the list that match no row in the database?
LastMod: 2024-01-23
---

# How do you filter a list to return only items from the list that match no row in the database?

## Description

The `WhereBulkNotContainsFilterList` method lets you filter a list by only returning items from the list that doesn't exists in the database. It's the invese of the [WhereBulkContainsFilterList](/where-bulk-contains-filter-list) method.

### Example

```csharp
// return all customers from the 'deserializedCustomers' that doesn't exist in the database
var existingCustomers = context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers).ToList();

// from the existing list, you can retrieve all existing customers by excluding non-existing ones without fetching the database this time.
var notExistingCustomers = deserializedCustomers.Exclude(existingCustomer);
```

## Scenario

Filtering a list to know which items already exist or not can sometimes be very useful.

For example, let's say that you want to split the `insert` and `update` logic as they have very different application rules. Instead of using the [Bulk Merge](/bulk-merge) method, you can now split the list with existing and non-existing items and use the [Bulk Insert](/bulk-insert) and [Bulk Update](/bulk-update) methods from both lists.

```csharp
// return all customers from the 'deserializedCustomers' that exists in the database
var notExistingCustomers = context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers).ToList();

// from the existing list, you can retrieve all non-existing customers by excluding them without fetching the database this time.
var existingCustomers = deserializedCustomers.Exclude(existingCustomer);

context.BulkInsert(notExistingCustomers);
context.BulkUpdate(existingCustomers);
```

## FAQ

- [How to use the method WhereBulkNotContainsFilterList?](#how-to-use-the-method-wherebulknotcontainsfilterlist)
- [What is the difference between the method WhereBulkNotContainsFilterList and WhereBulkNotContains?](#what-is-the-difference-between-the-method-wherebulknotcontainsfilterlist-and-wherebulknotcontains)
- [Where can I learn more about the method WhereBulkNotContainsFilterList?](#where-can-i-learn-more-about-the-method-wherebulknotcontainsfilterlist)

## How to use the method WhereBulkNotContainsFilterList?

The most basic scenario is passing a list to the `WhereBulkNotContainsFilterList` method.

The `WhereBulkNotContainsFilterList` method will filter the list to return only item that doesn't exists in the database.

```csharp
// The `JOIN` statement will use the default entity key if none is provided (CustomerID)
var customers = context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers);

// You can specify a custom `JOIN` clause with one or many properties using a `Lambda Expression`
var customers = context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers, x => x.Code);

// You can specify a custom `JOIN` clause with one or many properties using a `List<string>`
var customers = context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers, new List<string> { "Code" });

// You can specify a custom `JOIN` clause with one or many properties using a `params string[]`
var customers = context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers, "Code");
```

[Try it](https://dotnetfiddle.net/EOuxdJ)

The `WhereBulkNotContainsFilterListAsync` method is also supported.

## What is the difference between the method WhereBulkNotContainsFilterList and WhereBulkNotContains?

The `WhereBulkNotContainsFilterList` method is similar to the `WhereNotBulkContains` method, but what is returned is very different.

- The `WhereBulkContainsFilterList` method returns items from the list that match no row in the database.
- The `WhereBulkContains` method returns entities from the database that match no item in the list

So, the method you use depends on whether you want to filter the current list or instead return entities from the database.

## Where can I learn more about the method WhereBulkNotContainsFilterList?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.

## Related Solutions

- [WhereBulkContainsFilterList](/where-bulk-contains-filter-list)
- [WhereBulkContains](/where-bulk-contains)
- [WhereBulkNotContains](/where-bulk-not-contains)
- [BulkRead](/bulk-read)