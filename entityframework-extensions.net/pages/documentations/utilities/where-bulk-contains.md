# How to filter entities contained from an existing list with Entity Framework?

## Description

The `WhereBulkContains` method lets you filter a LINQ query by including all items from an existing list

### Example

```csharp
var customers = context.Customers.WhereBulkContains(deserializedCustomers);
```

## Scenario

Filtering entities by including items from an existing list is a common scenario.

For example, you want to retrieve customers to update properties you retrived for a list of `Customer` that you have previously deserialized from a JSON file.

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
- [Can I use WhereBulkContains method with millions of items?](#can-i-use-wherebulkcontains-method-with-millions-of-items)
- [How to join different properties than the entity key?](#how-to-join-different-properties-than-the-entity-key)
- [Can I use WhereBulkContains after a Where?](#can-i-use-wherebulkcontains-after-a-where)
- [Can I use WhereBulkContains with UpdateFromQuery and DeleteFromQuery?](#can-i-use-wherebulkcontains-with-updatefromquery-and-deletefromquery)
- [Do WhereBulkContains faster than Contains method?](#)
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

## What kind of list is supported?

All kinds of lists are supported. The only requirement is that your list is a basic type or must contain a property with the same name as the key:

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

## Can I use WhereBulkContains with millions of items?

Yes, you can use the `WhereBulkContains` method with an unlimited amount of items.

Under the hood, we create a temporary table and populated it with our very fast [BulkInsert](bulk-insert) method. Then we use this temporary table to perform an `INNER JOIN` statement.

## How to join different properties than the entity key?

By default, we create the join using the entity key, but you can choose one or many properties by passing:

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

## Can I use WhereBulkContains after a Where?

Yes, the `WhereBulkContains` is like any other LINQ method. You can chain any linq method, even a `Where` with a contains method.

```csharp
var customers = context.Customers.Where(x => x.CustomerID >= 2).WhereBulkContains(deserializedCustomers);
```

[Try it](https://dotnetfiddle.net/A7eSmW)

## Can I use WhereBulkContains with UpdateFromQuery and DeleteFromQuery?

The `WhereBulkContains` method is compatible with some of our other methods such as:
- [UpdateFromQuery](/update-from-query)
- [DeleteFromQuery](/delete-from-query)
- [InsertFromQuery](/insert-from-query)

```csharp
context.Customers.WhereBulkContains(deserializedCustomers).UpdateFromQuery(x => new { FirstName = "UpdateFromQuery" });
context.Customers.WhereBulkNotContains(deserializedCustomers).DeleteFromQuery();
context.Customers.WhereBulkContains(deserializedCustomers).InsertFromQuery(x => new { x.Code, FirstName = "Copied", x.LastName, x.Email });
```

[Try it](https://dotnetfiddle.net/B9c0uA)

## Do WhereBulkContains faster than Contains method?

In most scenarios, the answer will probably be no. The `Contains` method is faster due to simply using a very fast`IN (...)` statement.

The `WhereBulkContains` method is also very fast, but the main advantage is his flexibility by supporting multiple scenarios that the `Contains` method doesn't support.

## What are the limitations?

We currently only support SQL Server.
