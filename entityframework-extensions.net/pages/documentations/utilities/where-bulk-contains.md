# How to filter entities contained from an existing list with Entity Framework?

## Description

Filtering entities by including items from an existing list is a common scenario.

A frequent solution is using the `Contains` methods such as:

```csharp
var customerIds = customersDto.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIds.Contains(x.CustomerID)).ToList();
```

However, this solution has many limitations such as:

- support basic types only such as `int` or `guid`
- the list of `ids` is limited due to SQL limitations
- and it doesn't support surrogate key or other more complex scenarios

The `WhereBulkContains` method lets you filter a query by including all entities from the list. It's doesn't have any limitation that the `Contains` method has.

## FAQ

- [How to use the method WhereBulkContains?](#how-to-use-the-method-wherebulkcontains)
- [What kind of list is supported?](#what-kind-of-list-is-supported)
- [Can I use WhereBulkContains method with millions of items?](#can-i-use-wherebulkcontains-method-with-millions-of-items)
- [How to join different properties than the entity key?](#how-to-join-different-properties-than-the-entity-key)
- [What are the limitations?](#what-are-the-limitations)

## How to use the method WhereBulkContains?

The most basic scenario is passing a `DTO` list to the `WhereBulkContains` method.

The method will filter entities to include those contained in the `DTO` list.

```csharp
var customers = context.Customers.WhereBulkContains(customersDto).ToList();
```

## What kind of list is supported?

The list could be of any type. The only requirement is that your list is a basic type or must contain a property/field with the same name as the key.

- Basic type such as `List<int>` and `List<Guid>`
- Entities list such as `List<Customer>`
- DTO list such as `List<CustomerDTO>`
- Anonymous list
- Expando Object list

```csharp
// example coming soon
```

## Can I use WhereBulkContains with millions of items?

Yes, you can use the `WhereBulkContains` method with an unlimited amount of items.

Under the hood, we create a temporary table and populated it with our very fast [BulkInsert](bulk-insert) method. Then we use this temporary table to perform an `INNER JOIN` statement.

## How to join different properties than the entity key?

By default, we create the join using the entity key, but you can choose one or many properties by passing:

- A lambda expression
- A list of string

```csharp
// example coming soon
```

## What are the limitations?

We currently only support SQL Server.