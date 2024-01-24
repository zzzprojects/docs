---
Name: How to filter entities contained from an existing list with Entity Framework?
LastMod: 2023-03-01
---

# How to filter entities contained from an existing list with Entity Framework?

<iframe width="560" height="315" src="https://www.youtube.com/embed/9xVaL63LOsY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Description

The `WhereBulkContains` method lets you filter a LINQ query by including all items from an existing list.

### Example

```csharp
var customers = context.Customers.WhereBulkContains(deserializedCustomers).ToList();

var activeCustomers = context.Customers.Where(x => x.IsActive).WhereBulkContains(deserializedCustomers).ToList();
```

## Scenario

Filtering entities using an existing list is a common scenario.

For example, you deserialize a JSON file into a list of `Customer` with the `CustomerID` and a few other properties populated. Then you want to retrieve those customers from the database to update those properties.

A frequent solution is using the `Contains` methods such as:

```csharp
var customerIds = deserializedCustomers.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIds.Contains(x.CustomerID)).ToList();
```

It works great and is easy to use.

However, this solution has some limitations such as:

- It only supports basic types like `int` or `guid`
- The list of `ids` is limited due to SQL limitations
- It doesn't support surrogate key (more than one key) or other complex scenarios

The `WhereBulkContains` method lets you filter a query by including all entities from the list. It's doesn't have any of the `Contains` method limitations.

## FAQ

- [How to use the method WhereBulkContains?](#how-to-use-the-method-wherebulkcontains)
- [What kind of list is supported?](#what-kind-of-list-is-supported)
- [Can I use WhereBulkContains after a Where?](#can-i-use-wherebulkcontains-after-a-where)
- [Can I use the WhereBulkContains method with millions of items?](#can-i-use-the-wherebulkcontains-with-million-of-items)
- [How can I use a custom key?](#how-can-i-use-a-custom-key)
- [Can I use WhereBulkContains with Batch Operations?](#can-i-use-wherebulkcontains-with-batch-operations)
- [What are Contains method limitations?](#what-are-contains-method-limitations)
- [Do WhereBulkContains faster than Contains method?](#do-wherebulkcontains-faster-than-contains-method)
- [What is the difference between the method WhereBulkContains, WhereBulkNotContains, and BulkRead?](#what-is-the-difference-between-the-method-wherebulkcontains-wherebulknotcontains-and-bulkread)
- [What are the limitations?](#what-are-the-limitations)

## How to use the method WhereBulkContains?

The most basic scenario is passing a list to the `WhereBulkContains` method.

The `WhereBulkContains` method will filter entities to include those contained in the list.

```csharp
// The `JOIN` statement will use the default entity key if none is provided (CustomerID)
var customers = context.Customers.WhereBulkContains(deserializedCustomers);

// You can specify a custom `JOIN` clause with one or many properties using a `Lambda Expression`
var customers = context.Customers.WhereBulkContains(deserializedCustomers, x => x.Code);

// You can specify a custom `JOIN` clause with one or many properties using a `List<string>`
var customers = context.Customers.WhereBulkContains(deserializedCustomers, new List<string> { "Code" });

// You can specify a custom `JOIN` clause with one or many properties using a `params string[]`
var customers = context.Customers.WhereBulkContains(deserializedCustomers, "Code");
```

[Try it](https://dotnetfiddle.net/DEDiuR)

The `WhereBulkContainsAsync` method is also supported.

## What kind of list is supported?

All kinds of lists are supported. The only requirement is that your list is a basic type or contains a property with the same name as the key:

- Basic type such as `List<int>` and `List<Guid>`
- Entity Type such as `List<Customer>`
- Anonymous Type
- Expando Object list
 
```csharp
{
	// - Basic type such as `List<int>` and `List<Guid>`
	var ids = deserializedCustomers.Select(x => x.CustomerID).ToList();
	var customers = context.Customers.WhereBulkContains(ids);
}
{
	// - Entity Type such as `List<Customer>`
	var customers = context.Customers.WhereBulkContains(deserializedCustomers);
}
{
	// - Anonymous Type
	var anonymousIds = deserializedCustomers.Select(x => new { x.CustomerID }).ToList();
	var customers = context.Customers.WhereBulkContains(anonymousIds);
}
{
	// Expando Object list
	var expandos = new List<ExpandoObject>();
	deserializedCustomers.ForEach(x => {
		dynamic expando = new ExpandoObject();
		expando.CustomerID = x.CustomerID;
		expandos.Add(expando);
	});
	var customers = context.Customers.WhereBulkContains(expandos);
}
```

[Try it](https://dotnetfiddle.net/A7X89y)

The `WhereBulkContainsAsync` method is also supported.

## Can I use WhereBulkContains after a Where?

Yes, the `WhereBulkContains` is an extension method that you can chain like any other LINQ method. You can chain it before or after the `Where` or any other LINQ methods.

```csharp
var customers = context.Customers.Where(x => x.CustomerID >= 2).WhereBulkContains(deserializedCustomers);
```

[Try it](https://dotnetfiddle.net/A7eSmW)

## Can I use the WhereBulkContains with million of items?

Yes, you can use the `WhereBulkContains` method with an unlimited amount of items.

Under the hood, we create a temporary table and populated it with our very fast [BulkInsert](bulk-insert) method. Then we use this temporary table to perform an `INNER JOIN` statement.

## How can I use a custom key?

By default, we create the join using the entity key, but you can choose a custom key with one or many properties by passing:

- A lambda expression
- A list of string

```csharp
// You can specify a custom `JOIN` clause with one or many properties using a `Lambda Expression`
var customers = context.Customers.WhereBulkContains(deserializedCustomers, x => x.Code);

// You can specify a custom `JOIN` clause with one or many properties using a `List<string>`
var customers = context.Customers.WhereBulkContains(deserializedCustomers, new List<string> { "Code" });

// You can specify a custom `JOIN` clause with one or many properties using a `params string[]`
var customers = context.Customers.WhereBulkContains(deserializedCustomers, "Code");
```

[Try it](https://dotnetfiddle.net/DEDiuR)

## Can I use WhereBulkContains with Batch Operations?

The `WhereBulkContains` method is compatible with our batch methods:
- [UpdateFromQuery](/update-from-query)
- [DeleteFromQuery](/delete-from-query)
- [InsertFromQuery](/insert-from-query)

```csharp
context.Customers.WhereBulkContains(deserializedCustomers).UpdateFromQuery(x => new { FirstName = "UpdateFromQuery" });
context.Customers.WhereBulkNotContains(deserializedCustomers).DeleteFromQuery();
context.Customers.WhereBulkContains(deserializedCustomers).InsertFromQuery(x => new { x.Code, FirstName = "Copied", x.LastName, x.Email });
```

[Try it](https://dotnetfiddle.net/B9c0uA)

## What are Contains method limitations?

Has said previously, the `Contains` method already work great but also have his own limitations:

- It only supports basic types like `int` or `guid`
- The list of `ids` is limited due to SQL limitations
- It doesn't support surrogate key (more than one key) or other complex scenarios

The `WhereBulkContains` doesn't have any of those limitations.

## Do WhereBulkContains faster than Contains method?

In most scenarios, the answer will probably be no. The `Contains` method is faster due to simply using a very basic `IN (...)` statement.

The `WhereBulkContains` method is also very fast, but the main advantage is his flexibility by supporting:

- An unlimited amount of items
- Any kind of list
- Custom key/surrogate key

## What is the difference between the method WhereBulkContains, WhereBulkNotContains, and BulkRead?

The `WhereBulkNotContains` method is similar to the `WhereBulkContains` method, but it filters entities not contained (`exclude`) instead of contained (`include`):

- The `WhereBulkContains` method filters entities to include entities from the list (`INNER JOIN` statement) 
- The `WhereBulkNotContains` method filters entities to exclude entities from the list (`WHERE NOT EXISTS` statement).

As for the `BulkRead` method, under the hood calls the `WhereBulkContains` method followed by the `ToList` or `ToListAsync` method.

```csharp
// Using the BulkRead method is exactly like doing the following code:
var customers = context.Customers.WhereBulkContains(deserializedCustomers).ToList();
```

So all three methods are very similar but serve different purpose.

## What are the limitations?

We currently only support SQL Server and PostgreSQL.

We do not support inheritance such as `TPC`, `TPH`, and `TPT`

## Related Solutions

- [WhereBulkNotContains](/where-bulk-not-contains)
- [BulkRead](/bulk-read)
- [WhereBulkContainsFilterList](/where-bulk-contains-filter-list)
- [WhereBulkNotContainsFilterList](/where-bulk-not-contains-filter-list)
- Batch Methods
   - [UpdateFromQuery](/update-from-query)
   - [DeleteFromQuery](/delete-from-query)
   - [InsertFromQuery](/insert-from-query)