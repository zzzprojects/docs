# How to filter entities not contained from an existing list with Entity Framework?

## Description

Filtering entities by excluding items from an existing list can sometimes be very useful.

For example, you receive a `DTO` list with the `CustomerID` populated, and you want to retrieve customers not contained in this list to deleted them.

The `WhereBulkNotContains` method lets you filter a query to exclude all entities from any list type (basic type, entities, DTO, anonymous, expando) and specify a custom join with one or more properties. The method is powerful because you can use it with an unlimited amount of items.

The `WhereBulkNotContains` method is similar to the `WhereBulkContains` method but filter entities not contained (`exclude`) instead of contained (`include`):

- The `WhereBulkContains` method filter entities to include entities from the list (`INNER JOIN` statement) 
- The `WhereBulkNotContains` method filter entities to exclude entities from the list (`WHERE NOT EXISTS` statement).

## FAQ

- [How to use the method WhereBulkNotContains?](#how-to-use-the-method-wherebulknotcontains)
- [Where can I learn more about the method WhereBulkNotContains?](#where-can-i-learn-more-about-the-method-wherebulknotcontains)

## How to use the method WhereBulkNotContains?

The most basic scenario is passing a `DTO` list to the `WhereBulkNotContains` method.

The method will filter entities to exclude those contained in the `DTO` list.

```csharp
var customers = context.Customers.WhereBulkNotContains(customersDto).ToList();
```

## Where can I learn more about the method WhereBulkNotContains?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.