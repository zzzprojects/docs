# How to filter entities contained from an existing list with Entity Framework?

## Description

Filtering entities by including items from an existing list is a common scenario.

A common solution is using the `Contains` methods such as:

```csharp
var customerIds = customersDto.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIds.Contains(x.CustomerID)).ToList();
```

However, this solution has many limitations such as:

- support basic types such as `int` or `guid` only
- the list of `ids` is limited due to SQL parameter limits
- and it doesn't support surrogate key or other more complex scenarios

The `WhereBulkContains` method lets you filter a list of entities in any way you would wish.

## FAQ

- [How to use the method WhereBulkContains?](#how-to-use-the-method-wherebulkcontains)
- [How to join different properties than the entity key?](#how-to-join-different-properties-than-the-entity-key)
- [What kind of list is supported?](#what-kind-of-list-is-supported)
- [What are the limitations?](#what-are-the-limitations)

## How to use the method WhereBulkContains?

The most basic scenario is passing a list to the `WhereBulkContains` method:

```csharp
// example coming soon
```

The list will be used for the `INNER JOIN` statement.

## How to join different properties than the entity key?

A common scenario is having to retrieve a list of customer thought their code because you don't have the information about their IDs.

The `WhereBulkContains` method allow you to specify one or many properties which will be used to perform the `JOIN` statement.

You can use a lambda expression or directly properties names.

### Example with a single property

```csharp
// example coming soon
```

### Example with many properties

```csharp
// example coming soon
```

## What kind of list is supported?

The list could be of any type, you just need to ensure that a property or field exists for the key or the join you provided.

- Basic type such as `List<int>` and `List<Guid>`
- Entities list such as `List<Customer>`
- DTO list such as `List<CustomerDTO>`
- Anonymous list
- Expando Object list

```csharp
// example coming soon
```

## What are the limitations?

We currently only support SQL Server at the moment.