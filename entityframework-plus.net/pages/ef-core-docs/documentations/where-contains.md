---
Title: EF Core WhereContains and WhereNotContains
MetaDescription: Learn how to filter EF Core queries with WhereContains and WhereNotContains by using entities, keys, anonymous types, and automatic strategies for large lists.
LastMod: 2026-08-18
---

# EF Core WhereContains and WhereNotContains

Filtering a query by using values from an existing list is a common scenario in Entity Framework Core.

The built-in `Contains` method works well when you have a list of primitive values such as customer IDs:

```csharp
var customerIDs = customers.Select(x => x.CustomerID).ToList();

var existingCustomers = context.Customers
    .Where(x => customerIDs.Contains(x.CustomerID))
    .ToList();
```

However, you sometimes already have a list of entities, need to match a custom property, or need to match more than one property.

Entity Framework Plus provides two free methods for these scenarios:

- `WhereContains`: Returns entities that match an item in your list.
- `WhereNotContains`: Returns entities that do not match any item in your list.

These methods are designed for small and medium lists. For large lists, you can use [`WhereBulkContains`](https://entityframework-extensions.net/where-bulk-contains) and [`WhereBulkNotContains`](https://entityframework-extensions.net/where-bulk-not-contains) from Entity Framework Extensions.

> Note: `WhereContains` and `WhereNotContains` require EF Core 5 or later.

- [WhereContains Example](#wherecontains-example)
- [WhereNotContains Example](#wherenotcontains-example)
- [Using a List of Primitive Values](#using-a-list-of-primitive-values)
- [Using a List of Entities](#using-a-list-of-entities)
- [Using a Custom Key](#using-a-custom-key)
- [Using a Composite Key](#using-a-composite-key)
- [Chaining with Other LINQ Methods](#chaining-with-other-linq-methods)
- [How the Resolution Strategy Works](#how-the-resolution-strategy-works)
- [WhereContainsManager Options](#wherecontainsmanager-options)
- [When to Use WhereBulkContains](#when-to-use-wherebulkcontains)
- [Summary](#summary)

## WhereContains Example

The `WhereContains` method filters the query to return only entities that match an item in your list.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var customers = new List<Customer>
{
    new Customer { CustomerID = 1 },
    new Customer { CustomerID = 3 },
    new Customer { CustomerID = 5 }
};

var existingCustomers = context.Customers
    .WhereContains(customers)
    .ToList();
```

When no key is specified, `WhereContains` uses the entity key configured in the EF Core model.

In this example, only customers with a `CustomerID` of `1`, `3`, or `5` are returned.

## WhereNotContains Example

The `WhereNotContains` method performs the opposite filter. It returns only entities that do not match any item in your list.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var customers = new List<Customer>
{
    new Customer { CustomerID = 1 },
    new Customer { CustomerID = 3 },
    new Customer { CustomerID = 5 }
};

var otherCustomers = context.Customers
    .WhereNotContains(customers)
    .ToList();
```

In this example, customers with a `CustomerID` of `1`, `3`, or `5` are excluded.

## Using a List of Primitive Values

You can pass a list of primitive values such as `int`, `long`, `Guid`, or `string`.

```csharp
var customerIDs = new List<int> { 1, 3, 5 };

var customers = context.Customers
    .WhereContains(customerIDs)
    .ToList();
```

When a primitive list is used, the values are matched against the entity key by default.

You can use `WhereNotContains` with the same list:

```csharp
var customers = context.Customers
    .WhereNotContains(customerIDs)
    .ToList();
```

## Using a List of Entities

You can directly pass a list of entities. You do not need to create a separate list containing only their keys.

```csharp
var importedCustomers = GetImportedCustomers();

var customers = context.Customers
    .WhereContains(importedCustomers)
    .ToList();
```

This is useful when entities have been deserialized from JSON, imported from a file, or received from another part of your application.

Only the values used for matching are added to the query. The entities do not need to be tracked by the current `DbContext`.

## Using a Custom Key

By default, the entity key configured in the EF Core model is used. You can also specify a different property to use for matching.

```csharp
var importedCustomers = GetImportedCustomers();

var customers = context.Customers
    .WhereContains(importedCustomers, x => x.Code)
    .ToList();
```

In this example, customers are matched by `Code` instead of `CustomerID`.

The same custom key can be used with `WhereNotContains`:

```csharp
var customers = context.Customers
    .WhereNotContains(importedCustomers, x => x.Code)
    .ToList();
```

## Using a Composite Key

You can use an anonymous type to match more than one property.

```csharp
var customerKeys = importedCustomers
    .Select(x => new
    {
        x.CompanyID,
        x.Code
    })
    .ToList();

var customers = context.Customers
    .WhereContains(customerKeys, x => new
    {
        x.CompanyID,
        x.Code
    })
    .ToList();
```

Both `CompanyID` and `Code` must match the same item from the list.

This is useful for composite keys and scenarios where a unique value is created from multiple properties.

## Chaining with Other LINQ Methods

`WhereContains` and `WhereNotContains` return an `IQueryable<T>`, so you can chain them with other LINQ methods.

```csharp
var customers = context.Customers
    .Where(x => x.IsActive)
    .WhereContains(importedCustomers)
    .OrderBy(x => x.Name)
    .ToList();
```

The query is not executed until you call a method such as `ToList`, `FirstOrDefault`, or their asynchronous equivalent.

You can also add the method before another `Where` condition:

```csharp
var customers = context.Customers
    .WhereContains(importedCustomers)
    .Where(x => x.IsActive)
    .ToList();
```

## How the Resolution Strategy Works

`WhereContains` and `WhereNotContains` analyze the list and automatically create a query that EF Core can translate.

Depending on your configuration, the methods can use one of the following resolutions:

- **Contains resolution:** Uses a `Contains` expression. This is normally the most efficient resolution for a lower number of values.
- **WHERE Any resolution:** Uses an `Any`-based expression. You can force this resolution by enabling `DisableWhereContainsResolution`.
- **WhereBulk resolution:** Uses `WhereBulkContains` or `WhereBulkNotContains` when `UseWhereBulkSmart` is enabled and the number of values reaches `MinValuesForWhereBulk`.

The WhereBulk resolution is supported only for SQL Server and PostgreSQL. It uses a paid feature from Entity Framework Extensions and requires a valid license.

## WhereContainsManager Options

You can use the static `WhereContainsManager` class to globally configure how `WhereContains` and `WhereNotContains` are resolved.

```csharp
WhereContainsManager.UseWhereBulkSmart = true;
WhereContainsManager.DisableWhereContainsResolution = false;
WhereContainsManager.MinValuesForWhereBulk = 4000;
```

These options are static and affect all `WhereContains` and `WhereNotContains` queries in the application.

### UseWhereBulkSmart

The `UseWhereBulkSmart` option enables automatic switching to `WhereBulkContains` or `WhereBulkNotContains` when the number of values reaches `MinValuesForWhereBulk`.

```csharp
WhereContainsManager.UseWhereBulkSmart = true;
```

The default value is `false`.

This option:

- Is supported only for SQL Server and PostgreSQL.
- Uses a paid feature from Entity Framework Extensions.
- Requires a valid Entity Framework Extensions license.
- Uses `WhereBulkContains` for a `WhereContains` query.
- Uses `WhereBulkNotContains` for a `WhereNotContains` query.

This option is useful when the number of values can vary significantly. Small and medium lists can use the regular EF Plus resolution, while large lists can automatically use the more scalable bulk resolution.

### DisableWhereContainsResolution

The `DisableWhereContainsResolution` option disables the `Contains` resolution for `WhereContains` and `WhereNotContains`.

```csharp
WhereContainsManager.DisableWhereContainsResolution = true;
```

When enabled, the WHERE `Any` resolution is used instead.

The default value is `false`.

You normally do not need to enable this option. It is available for providers or scenarios where the query generated by the `Contains` resolution is not appropriate.

### ContainsMaxItemsForParameters

> Note: This option is available only with EF Core 10 or later.

The `ContainsMaxItemsForParameters` option sets the maximum number of items for which the `Contains` resolution uses SQL parameters with SQL Server.

```csharp
WhereContainsManager.ContainsMaxItemsForParameters = 200;
```

The default value is `200`.

When the number of items exceeds this value, the values are inlined in the SQL instead of being added as SQL parameters.

This option applies only to SQL Server with EF Core 10 or later. Other providers and EF Core versions earlier than EF Core 10 already inline these values.

### MinValuesForWhereBulk

The `MinValuesForWhereBulk` option sets the minimum number of values required to switch to `WhereBulkContains` or `WhereBulkNotContains` when `UseWhereBulkSmart` is enabled.

```csharp
WhereContainsManager.UseWhereBulkSmart = true;
WhereContainsManager.MinValuesForWhereBulk = 4000;
```

The default value is `4000`.

For example:

- A list containing fewer than `4000` values uses the regular EF Plus resolution.
- A list containing `4000` values or more uses the WhereBulk resolution.

This option applies only to SQL Server and PostgreSQL. The WhereBulk resolution uses a paid Entity Framework Extensions feature and requires a valid license.

> Note: The threshold is based on the total number of values added to the query, not only the number of items in the list. An item matched with a composite key can add more than one value.

## When to Use WhereBulkContains

`WhereContains` and `WhereNotContains` are usually the best choice for small and medium lists because they are simple and free.

For large lists, consider using [`WhereBulkContains`](https://entityframework-extensions.net/where-bulk-contains) or [`WhereBulkNotContains`](https://entityframework-extensions.net/where-bulk-not-contains).

| Scenario | Recommended Method |
| --- | --- |
| Small or medium list | `WhereContains` or `WhereNotContains` |
| Large list with SQL Server or PostgreSQL | `WhereBulkContains` or `WhereBulkNotContains` |
| List size changes significantly | Enable `UseWhereBulkSmart` |
| Free solution required | `WhereContains` or `WhereNotContains` |
| Very large list or database parameter limitations | `WhereBulkContains` or `WhereBulkNotContains` |

The bulk methods insert the values into a temporary table and use this table to filter the query. This approach is more scalable for thousands or millions of values, but it requires Entity Framework Extensions and a valid license.

## Summary

In this article, you learned how to use `WhereContains` and `WhereNotContains` to filter an EF Core query by using an existing list.

- Use `WhereContains` to return entities that match an item in your list.
- Use `WhereNotContains` to exclude entities that match an item in your list.
- Pass primitive values, entities, or values for a custom or composite key.
- Chain the methods with other LINQ operators because they return `IQueryable<T>`.
- Configure the global resolution behavior through `WhereContainsManager`.
- Enable `UseWhereBulkSmart` to automatically use a paid bulk resolution for large lists with SQL Server or PostgreSQL.
- Use `WhereBulkContains` or `WhereBulkNotContains` directly when you already know that the list will be large.

These methods provide an easier way to filter by existing objects or multiple values while keeping the query inside the EF Core pipeline.
