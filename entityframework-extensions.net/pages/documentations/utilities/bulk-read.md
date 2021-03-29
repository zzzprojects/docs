# How to read entities contained from an existing list with Entity Framework?

## Description

Reading entities using an existing list is a common scenario.

For example, you receive a `DTO` (Domain Transfer Object) list with the `CustomerID` and some other properties populated, and you want to retrieve those customers from the database to update them.

A frequent solution is using the `Contains` method to retrieves those customers such as:

```csharp
var customerIds = customersDto.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIds.Contains(x.CustomerID)).ToList();
```

However, this solution has several limitations, as explained [here](/where-bulk-contains).

The `BulkRead` method has many advantages:
 - Allow using any list type (basic type, entity type, anonymous type, expando object)
 - Allow using an unlimited amount of items.
 - Allow specifying a custom join with one or many properties.

The `BulkRead` is an immediate method (return a `List<T>`). Under the hood, it calls the `WhereBulkContains` method followed by the `ToList()` method.

```csharp
var list1 = context.Customers.BulkRead(customersDto);

// BulkRead is exactly like doing the following code:
var list2 = context.Customers.WhereBulkContains(customersDto).ToList();
```

## FAQ

- [How to use the method BulkRead?](#how-to-use-the-method-bulkread)
- [Where I can learn more about the method BulkRead?](#where-can-i-learn-more-about-the-method-bulkread)

## How to use the method BulkRead?

The most basic scenario is passing a `DTO` list to the `BulkRead` method.

The method will retrieve entities from the database contained in the `DTO` list.

```csharp
// Use the entity type key if none is provided (CustomerID)
var customers = context.Customers.BulkRead(customersDto);

//  Allow specifying a custom join with one or many properties.
var customers = context.Customers.BulkRead(customersDto, x => x.Code);
```
## Where can I learn more about the method BulkRead?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.