---
Name: How to filter entities not contained from an existing list with Entity Framework?
LastMod: 2023-03-01
---

# How to filter entities not contained from an existing list with Entity Framework?

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceAiw-fOU_c" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Description

The `WhereBulkNotContains` method lets you filter a LINQ query by excluding all items from an existing list.

### Example

```csharp
var customers = context.Customers.WhereBulkNotContains(deserializedCustomers);
```

## Scenario

Filtering entities by excluding all items from an existing list can sometimes be very useful.

For example, you deserialize a JSON file into a list of `Customer` with the `CustomerID` populated, and you want to retrieve customers not contained in this list to deleted them.

A frequent solution is using the `!Contains` method to retrieves those customers such as:

```csharp
var customerIds = deserializedCustomers.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => !customerIds.Contains(x.CustomerID)).ToList();
```

However, this solution has several limitations, as explained [here](/where-bulk-contains#what-are-contains-method-limitations).

The `WhereBulkNotContains` method has many advantages:
 - Allow using any list type (basic type, entity type, anonymous type, expando object)
 - Allow using an unlimited amount of items.
 - Allow specifying a custom join with one or many properties.

## FAQ

- [How to use the method WhereBulkNotContains?](#how-to-use-the-method-wherebulknotcontains)
- [What is the difference between the method WhereBulkNotContains and WhereBulkContains?](#what-is-the-difference-between-the-method-wherebulknotcontains-and-wherebulkcontains)
- [Where can I learn more about the method WhereBulkNotContains?](#where-can-i-learn-more-about-the-method-wherebulknotcontains)

## How to use the method WhereBulkNotContains?

The most basic scenario is passing a list to the `WhereBulkNotContains` method.

The `WhereBulkNotContains` method will filter entities to exclude those contained in the list.

```csharp
// The `JOIN` statement will use the default entity key if none is provided (CustomerID)
var customers = context.Customers.WhereBulkNotContains(deserializedCustomers);

// You can specify a custom `JOIN` clause with one or many properties using a `Lambda Expression`
var customers = context.Customers.WhereBulkNotContains(deserializedCustomers, x => x.Code);

// You can specify a custom `JOIN` clause with one or many properties using a `List<string>`
var customers = context.Customers.WhereBulkNotContains(deserializedCustomers, new List<string> { "Code" });

// You can specify a custom `JOIN` clause with one or many properties using a `params string[]`
var customers = context.Customers.WhereBulkNotContains(deserializedCustomers, "Code");
```

[Try it](https://dotnetfiddle.net/v2oSiJ)

The `WhereBulkNotContainsAsync` method is also supported.

## What is the difference between the method WhereBulkNotContains and WhereBulkContains?

The `WhereBulkNotContains` method is similar to the `WhereBulkContains` method, but it filters entities not contained (`exclude`) instead of contained (`include`):

- The `WhereBulkContains` method filters entities to include entities from the list (`INNER JOIN` statement) 
- The `WhereBulkNotContains` method filters entities to exclude entities from the list (`WHERE NOT EXISTS` statement).

## Where can I learn more about the method WhereBulkNotContains?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.

You can also find additional information on our [YouTube video](https://youtu.be/ceAiw-fOU_c).

## Related Solutions

- [WhereBulkContains](/where-bulk-contains)
- [BulkRead](/bulk-read)
- [WhereBulkContainsFilterList](/where-bulk-contains-filter-list)
- [WhereBulkNotContainsFilterList](/where-bulk-not-contains-filter-list)