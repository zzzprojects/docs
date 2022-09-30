---
Title: Execute C# Code at Runtime - Get Started with your First Evaluation
MetaDescription: Learn how to execute a C# expression at runtime with parameter, return type, and from a dynamic string expression.
---

# My First LINQ Dynamic

LINQ is an incredibly powerful tool for querying data. It allows developers to write code that is both expressive and easy to read. However, something that's really missing is using LINQ with dynamic expression.

Indeed, you can use LINQ for most of your queries, but what to do if you wish to allow your user to filter a list for a report dynamically? That's where LINQ Dynamic through the C# Eval Library starts to shine.

In this tutorial, you will learn how to use our library to:

- [Use LINQ Dynamic with a WhereDynamic method](#)
- [Use LINQ Dynamic with a WhereDynamic method with variables](#)
- [Use LINQ Dynamic with an Execute method](#)
- [Use LINQ Dynamic with other methods](#)

## Use LINQ Dynamic with a WhereDynamic method

First, you must add the `using System.Linq;` directive to see LINQ Dynamic extension methods. 

In this example, we assume a typical scenario where a developer uses EF Core and needs to filter a customer list depending on a custom filter entered by the end-user input.

That's normally impossible as the `Where` clause cannot have a dynamic expression, but by using the C# Eval Expression library, you can easily achieve this result with the `WhereDynamic` method:

```csharp
```

## Use LINQ Dynamic with a WhereDynamic method with variables

In the last example, we already saw how powerful the `WhereDynamic` method is. However, we sometimes also need to use it with variables.

For example, you can have some environment variables that will help to filter more easily. For instance, by having some predefined status such as 

- `IsActive`
- `IsDeleted`
- `IsSuspended`

Or some variable to make the expression simpler such as:

- `LastWeek`
- `LastMonth`
- `LastYear`

In this example, we will filter a customer list to return only active customers who have logged in since the last month.

```csharp
```

## Use LINQ Dynamic with an Execute method

The `Execute` method is the most flexible LINQ Dynamic method. This method allows you to execute anything. Multiple LINQ methods can be chained with the `Execute` method, which bring what you can let a user input do to another level.

In this example, we will let the user input doing literally everything he wants:

- Filtering the list
- Ordering the list
- Selecting columns he wants to return from the list

```csharp
```

## Use LINQ Dynamic with other methods

Almost all LINQ methods are supported:
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
   - SingleDynamic
   - SingleOrDefaultDynamic
   - SumDynamic
   - ToArrayDynamic
   - ToListDynamic

Those extension methods extend following types:

- `IEnumerable`
- `IEnumerable<T>`
- `IQueryable`
- `IQueryable<T>`

## Conclusion

In this getting started tutorial, you learned how to use LINQ Dynamic with the `WhereDynamic` method, the `Execute` method, and other existing methods.

If you already know the LINQ syntax, using LINQ Dynamic should be really quick to learn.

We recommend registering to our [newsletter](https://mailchi.mp/zzzprojects/eval_expression_newsletter) to stay updated with the latest released documentation.

