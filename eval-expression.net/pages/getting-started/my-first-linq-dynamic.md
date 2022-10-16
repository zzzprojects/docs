---
Title: Execute LINQ Dynamic Expression with List and EF Core
MetaDescription: Learn how to use LINQ Dynamic query to execute methods such as Select, OrderBy, or Where clause dynamically.
---

# My First LINQ Dynamic

LINQ is an incredibly powerful way of querying data. It allows developers to write code that is both expressive and easy to read. However, something that's missing is using LINQ with dynamic expression.

Indeed, you can use LINQ for most of your queries, but what to do if you wish to allow your user to dynamically filter a list for a report? That is when using LINQ Dynamic through the C# Eval Library starts to shine.

You can use LINQ Dynamic methods for `IEnumerable` and `IQueryable`. However, that also means you can use it for ORM, such as querying within Entity Framework Core and EF6.

In this tutorial, you will learn how to use our library to:

- [Use LINQ Dynamic with a WhereDynamic method](#use-linq-dynamic-with-a-wheredynamic-method)
- [Use LINQ Dynamic with a WhereDynamic method with variables](#use-linq-dynamic-with-a-wheredynamic-method-with-variables)
- [Use LINQ Dynamic with the Execute method](#use-linq-dynamic-with-the-execute-method)
- [Use LINQ Dynamic with other methods](#use-linq-dynamic-with-other-methods)

## Use LINQ Dynamic with a WhereDynamic method

To use our method, you first must add the `using System.Linq;` directive to see LINQ Dynamic extension methods.

In this example, we assume a typical scenario where a developer uses EF Core and needs to filter a customer list depending on a custom filter entered by the end-user input.

The `Where` clause cannot have a dynamic expression. However, the C# Eval Expression library lets you easily achieve this result using the `WhereDynamic` method.

In this example, we will filter customers in the database to return only active customers who have logged in since the last month.


```csharp
```

We added the `Dynamic` suffix to every one of our methods to avoid confusion.

## Use LINQ Dynamic with a WhereDynamic method with variables

In the last example, we already saw how powerful the `WhereDynamic` method is. However, we sometimes also need to use it with variables.

For example, you can have some environment variables that will help your user to filter more easily. For instance, by having some predefined status, such as:

- `IsActive`
- `IsDeleted`
- `IsSuspended`

Or some variable to make the expression simpler such as:

- `LastWeek`
- `LastMonth`
- `LastYear`

In this example, we will use the same filter as the previous example (return only active customers who have logged in since the last month), but this time, with the help of variables:

```csharp
```

## Use LINQ Dynamic with the Execute method

The `Execute` method is the most flexible LINQ Dynamic method. Not only can the user filter a query dynamically, but he can do whatever he wants in the expression.

In this example, we will query customers in the database and:

- Filter the customers to returns
- Order the returned customers
- Select columns that we return

```csharp
```

## Use LINQ Dynamic with other methods

Through this getting started about how to use LINQ Dynamic, we have seen a few methods, but almost all LINQ methods are supported:

- Deferred
   - DistinctDynamic
   - GroupByDynamic
   - OrderByDescendingDynamic
   - OrderByDynamic
   - ReverseDynamic
   - SelectDynamic
   - SelectManyDynamic
   - SkipWhileDynamic
   - TakeWhileDynamic
   - ThenByDescendingDynamic
   - ThenByDynamic
   - WhereDynamic
- Immediate
   - AllDynamic
   - AnyDynamic
   - CountDynamic
   - ElementAtDynamic
   - FirstDynamic
   - FirstOrDefaultDynamic
   - LastDynamic
   - LastOrDefaultDynamic
   - LongCountDynamic
   - MaxDynamic
   - MinDynamic
   - SelectToListDynamic
   - SelectManyToListDynamic
   - SingleDynamic
   - SingleOrDefaultDynamic
   - SumDynamic
   - ToArrayDynamic
   - ToListDynamic

You can use extension methods to extend the following types:

- `IEnumerable`
- `IEnumerable<T>`
- `IQueryable`
- `IQueryable<T>`

As we have seen in our example, you can also use LINQ Dynamic with EF Core.

## Conclusion

In this getting started tutorial, you learned how to use LINQ Dynamic with the `WhereDynamic` method, the `Execute` method, and the existence of all other supported dynamic methods.

If you already know how to use LINQ, using LINQ Dynamic should be very straightforward as the syntax is the same.