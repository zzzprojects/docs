---
Name: How to read entities contained from an existing list with Entity Framework?
LastMod: 2023-03-01
---

# How to read entities contained from an existing list with Entity Framework?

<iframe width="560" height="315" src="https://www.youtube.com/embed/6rDrB0I5Kyk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Description

The `BulkRead` method lets you filter a LINQ query by including all items from an existing list and then return the result immediately (`ToList`).

### Example

```csharp
var customers = context.Customers.BulkRead(deserializedCustomers);
```

## Scenario

Reading entities using an existing list is a common scenario.

For example, you deserialize a JSON file into a list of `Customer` with the `CustomerID` and a few other properties populated. Then you want to retrieve those customers from the database to update those properties.

A frequent solution is using the LINQ `Contains` method to retrieves those customers such as:

```csharp
var customerIDs = deserializedCustomers.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIDs.Contains(x.CustomerID)).ToList();
```

This solution works great most of the time. However, in some scenarios, you will hit one of the `Contains` method limitations, as explained [here](/where-bulk-contains#what-are-contains-method-limitations).

The `BulkRead` method has many advantages:
 - Allow using any list type (basic type, entity type, anonymous type, expando object)
 - Allow using an unlimited amount of items
 - Allow specifying a custom join with one or many properties

## FAQ

- [How to use the method BulkRead?](#how-to-use-the-method-bulkread)
- [What is the difference between the method BulkRead and WhereBulkContains?](#what-is-the-difference-between-the-method-bulkread-and-wherebulkcontains)
- [Where can I learn more about the method BulkRead?](#where-can-i-learn-more-about-the-method-bulkread)

## How to use the method BulkRead?

The most basic scenario is passing a list to the `BulkRead` method.

The `BulkRead` method will retrieve entities from the database contained in the list.

```csharp
// The `JOIN` statement will use the default entity key if none is provided (CustomerID)
var customers = context.Customers.BulkRead(deserializedCustomers);

// You can specify a custom `JOIN` clause with one or many properties using a `Lambda Expression`
var customers = context.Customers.BulkRead(deserializedCustomers, x => x.Code);

// You can specify a custom `JOIN` clause with one or many properties using a `List<string>`
var customers = context.Customers.BulkRead(deserializedCustomers, new List<string> {"Code"});

// You can specify a custom `JOIN` clause with one or many properties using a `params string[]`
var customers = context.Customers.BulkRead(deserializedCustomers, "Code");
```

[Try it](https://dotnetfiddle.net/TrBjjM)

The `BulkReadAsync` method is also supported.

## What is the difference between the method BulkRead and WhereBulkContains?

Both methods are part of EF Extensions and are the same minus one difference. The `BulkRead` is an immediate method (return a `List<T>`) while the `WhereBulkContains` is a deferred method (return an `IQueryable<T>`).

Under the hood, the `BulkRead` method calls the `WhereBulkContains` method followed by the `ToList` or `ToListAsync` method.

```csharp
// Using the BulkRead method is exactly like doing the following code:
var customers = context.Customers.WhereBulkContains(deserializedCustomers).ToList();
```

## Where can I learn more about the method BulkRead?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.

You can also find additional information on our [YouTube video](https://youtu.be/6rDrB0I5Kyk).

## Related Solutions

- [WhereBulkContains](/where-bulk-contains)
- [WhereBulkNotContains](/where-bulk-not-contains)
- [WhereBulkContainsFilterList](/where-bulk-contains-filter-list)
- [WhereBulkNotContainsFilterList](/where-bulk-not-contains-filter-list)