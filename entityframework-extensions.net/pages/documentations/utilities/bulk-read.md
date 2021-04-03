# How to read entities contained from an existing list with Entity Framework?

## Description

Reading entities using an existing list is a common scenario.

For example, you deserialize a JSON file into a list of `Customer` with the `CustomerID` and a few other properties populated, and you want to retrieve those customers from the database to update those properties.

A frequent solution is using the `Contains` method to retrieves those customers such as:

```csharp
var customerIds = deserializedCustomers.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIds.Contains(x.CustomerID)).ToList();
```

However, this solution has several limitations, as explained [here](/where-bulk-contains).

The `BulkRead` method has many advantages:
 - Allow using any list type (basic type, entity type, anonymous type, expando object)
 - Allow using an unlimited amount of items.
 - Allow specifying a custom join with one or many properties.

The `BulkRead` is an immediate method (return a `List<T>`). Under the hood, it calls the `WhereBulkContains` method followed by the `ToList()` method.

```csharp
var list1 = context.Customers.BulkRead(deserializedCustomers);

// BulkRead is exactly like doing the following code:
var list2 = context.Customers.WhereBulkContains(deserializedCustomers).ToList();
```

## FAQ

- [How to use the method BulkRead?](#how-to-use-the-method-bulkread)
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

## Where can I learn more about the method BulkRead?

You can learn more by reading the [WhereBulkContains](/where-bulk-contains) documentation.

Both methods have the same features and limitations.