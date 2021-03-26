# How to filter entities not contained from an existing list with Entity Framework?

## Description

Filtering entities by excluding items from an existing list can sometimes be very useful.

For example, you receive a `DTO` list with the `CustomerID` populated, and you want to retrieve customers not contained in this list to deleted them.

A frequent solution is using the `!Contains` method to retrieves those customers such as:

```csharp
var customerIds = customersDto.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => !customerIds.Contains(x.CustomerID)).ToList();
```

However, this solution has several limitations, as explained  [here](/where-bulk-contains).

The `WhereBulkNotContains` method has many advantages:
 - Allow using any list type (basic type, entity type, anonymous type, expando object)
 - Allow using an unlimited amount of items.
 - Allow specifying a custom join with one or many properties.

The `WhereBulkNotContains` method is similar to the `WhereBulkContains` method, but it filters entities not contained (`exclude`) instead of contained (`include`):

- The `WhereBulkContains` method filters entities to include entities from the list (`INNER JOIN` statement) 
- The `WhereBulkNotContains` method filters entities to exclude entities from the list (`WHERE NOT EXISTS` statement).

## FAQ

- [How to use the method WhereBulkNotContains?](#how-to-use-the-method-wherebulknotcontains)
- [Where can I learn more about the method WhereBulkNotContains?](#where-can-i-learn-more-about-the-method-wherebulknotcontains)

## How to use the method WhereBulkNotContains?

The most basic scenario is passing a `DTO` list to the `WhereBulkNotContains` method.

The method will filter entities to exclude those contained in the `DTO` list.

```csharp
// Use the entity type key if none is provided (CustomerID)
var customers = context.Customers.WhereBulkNotContains(customersDto).ToList();

//  Allow specifying a custom join with one or many properties.
var customers = context.Customers.WhereBulkNotContains(customersDto, x => x.Code).ToList();
```

## Where can I learn more about the method WhereBulkNotContains?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.
