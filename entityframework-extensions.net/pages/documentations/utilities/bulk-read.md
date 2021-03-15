# How to read entities contained from an existing list with Entity Framework?

## Description

Reading entities using an existing list such as a `DTO` (Domain Transfer Object) is a common scenario.

For example, you receive a `DTO` list with the `CustomerID` and some other properties populated, and you want to retrieve those customers from the database to update them.

The `BulkRead` method lets you retrieve entities from any list type (basic type, entities, DTO, anonymous, expando) and specify a custom join with one or more properties. The method is powerful because you can use it with an unlimited amount of items.

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
var customers = context.Customers.BulkRead(customersDto);
```
## Where can I learn more about the method BulkRead?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.