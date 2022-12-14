---
Name: LINQ Dynamic
---

# How to use LINQ with Dynamic Expression

Dynamically building a LINQ by adding a `Where` clause or `OrderBy` clause coming from user input is a very common scenario.

LINQ is a great addition to .NET, and is one of the most used features for ORM, such as Entity Framework, but it lacks the flexibility to create a dynamic query.

To overcome that limitation, there are 2 FREE common solutions (both from our company):

- [Dynamic LINQ](https://dynamic-linq.net/)
- C# Eval Expression

Dynamic LINQ library is free. It makes it very easy to customize your query through a string. One major advantage and disadvantage at the same time is it doesn't use a C# syntax but instead its homemade syntax. If you want to learn more about this library, we recommend reading his [documentation](https://dynamic-linq.net/overview)

Most of the time, the C# Eval Expression is the best solution for using LINQ Dynamic:

- Using LINQ Dynamic method is free (you don't have to purchase a license)
- Use the C# Language
- Doesn't "interpret" the expression, unlike other libraries, instead it parses the expression and calls the underlying LINQ method
- Easy to pass parameters via anonymous type or dictionary
- Easy to know when a `Dynamic` method is used (they have all the same suffix!)

Let me repeat one more time one of his advantages: **The C# Eval Expression is free** when using LINQ Dynamic method.

## Parameters

Before going deeper into the method subject, let's see how parameters can be passed through a LINQ Dynamic method. Like [Execute Methods](/eval-execute), you can pass parameter throughs:

- Using an Anonymous Type
- Using a Class Instance
- Using a Dictionary<string, TValue>
- Using an Expando Object
- Using parameter values

```csharp
```

## Method

Let's see first all LINQ methods available:

| Category  | Name                     | Description                                                                                                                                                                                                                             |
| --------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| \_Z       | Execute                  | The LINQ Execute method lets you use a dynamic string expression to execute any LINQ methods.                                                                                                                                            |
| Deferred  | DistinctDynamic          | The LINQ DistinctDynamic method returns distinct elements from a sequence.                                                                                                                                                              |
| Deferred  | GroupByDynamic           | The LINQ GroupByDynamic method groups the elements of a sequence. You can use a dynamic string expression to specify columns.                                                                                                           |
| Deferred  | OrderByDescendingDynamic | The LINQ OrderByDescendingDynamic method sorts the elements of a sequence in descending order. You can use a dynamic string expression to specify columns.                                                                               |
| Deferred  | OrderByDynamic           | The LINQ OrderByDynamic method sorts the elements of a sequence in ascending order. You can use a dynamic string expression to specify columns.                                                                                          |
| Deferred  | ReserveDynamic           | The LINQ ReserveDynamic method inverts the order of the elements in a sequence.                                                                                                                                                         |
| Deferred  | SelectDynamic            | The LINQ SelectDynamic projects each element of a sequence into a new form. You can use a dynamic string expression to select a specific column.                                                                                        |
| Deferred  | SelectManyDynamic        | The LINQ SelectManyDynamic projects each element of a sequence to an IEnumerable and flattens the resulting sequences into one sequence. You can use a dynamic string expression to select a specific column.                           |
| Deferred  | SkipWhileDynamic         | The LINQ SkipWhileDynamic method bypasses elements in a sequence as long as a specified condition is true and then returns the remaining elements. You can use a dynamic string expression to create the predicate.                     |
| Deferred  | TakeWhileDynamic         | The LINQ TakeWhileDynamic method returns elements from a sequence as long as a specified condition is true, and then skips the remaining elements. You can use a dynamic string expression to create the predicate.                     |
| Deferred  | ThenByDescendingDynamic  | The LINQ ThenByDescendingDynamic method performs a subsequent ordering of the element in a sequence in descending order. You can use a dynamic string expression to specify columns in the sorting.                                     |
| Deferred  | ThenByDynamic            | The LINQ ThenByDynamic method performs a subsequent ordering of the element in a sequence in ascending order. You can use a dynamic string expression to specify columns in the sorting.                                                |
| Deferred  | WhereDynamic             | The LINQ WhereDynamic method filters a sequence of values based on a predicate.  You can use a dynamic string expression to create the predicate.                                                                                       |
| Immediate | AllDynamic               | The LINQ AllDynamic method determines whether all elements of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                |
| Immediate | AnyDynamic               | The LINQ AnyDynamic method determines whether any element of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                 |
| Immediate | CountDynamic             | The LINQ CountDynamic method returns the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.                                                          |
| Immediate | ElementAtDynamic         | The LINQ ElementAtDynamic method returns the element at a specified index in a sequence.                                                                                                                                                |
| Immediate | FirstDynamic             | The LINQ FirstDynamic returns the first element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                                  |
| Immediate | FirstOrDefaultDynamic    | The LINQ FirstOrDefaultDynamic returns the first element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.                              |
| Immediate | LastDynamic              | The LINQ LastDynamic returns the last element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                                    |
| Immediate | LastOrDefaultDynamic     | The LINQ LastOrDefaultDynamic returns the last element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.                               |
| Immediate | LongCountDynamic         | The LINQ LongCountDynamic method returns an Int64 that represents the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.                             |
| Immediate | MaxDynamic               | The LINQ MaxDynamic returns the maximum value in a sequence. You can use a dynamic string expression to select a specific column.                                                                                                       |
| Immediate | MinDynamic               | The LINQ MinDynamic returns the minimum value in a sequence. You can use a dynamic string expression to select a specific column.                                                                                                       |
| Immediate | SelectToListDynamic      | The LINQ SelectDynamic projects each element of a sequence into a new form and create a List from it. You can use a dynamic string expression to select a specific column.                                                              |
| Immediate | SelectManyToListDynamic  | The LINQ SelectManyDynamic projects each element of a sequence to an IEnumerable and flattens the resulting sequences into one sequence, and creates a List from it. You can use a dynamic string expression to select a specific column. |
| Immediate | SingleDynamic            | The LINQ SingleDynamic returns a single, specific element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                        |
| Immediate | SingleOrDefaultDynamic   | The LINQ SingleDynamic returns a single, specific element of a sequence, or a default value if that element is not found. You can use a dynamic string expression to filter elements that satisfy the condition.                       |
| Immediate | SumDynamic               | The LINQ SumDynamic returns the sum of a sequence of numeric values. You can use a dynamic string expression to select a specific column.                                                                                               |
| Immediate | ToArrayDynamic           | The LINQ ToArrayDynamic creates an Array from an IEnumerable.                                                                                                                                                                            |
| Immediate | ToListDynamic            | The LINQ ToListDynamic creates a List from an IEnumerable.                                                                                                                                                                               |                                                                                                                                                                           |

## LINQ Execute Method

The LINQ Execute method lets you use a dynamic string expression to execute any LINQ methods.

That is probably the most powerful LINQ method, as it allows you to chain multiple LINQ expressions one after another one.

Keep in mind that you currently execute C# code, so you should call directly LINQ method (`Where`) and not LINQ dynamic method (`WhereDynamic`).

In this example, we will select a list of customers and dynamically perform a `Where` clause by their countries, then an OrderBy by their names, then by their last names.

```csharp
var list = new List<int>() { 1, 2, 3, 4, 5 };

var list2 = list.Execute<List<int>>("Where(x => x > 2).OrderBy(x => x).ToList()");
var list3 = list.Execute<List<int>>("Where(x => x > y).OrderBy(x => x).ToList()", new { y = 2 });
```

## LINQ DistinctDynamic Method

The LINQ DistinctDynamic method returns distinct elements from a sequence.

In this example, we will select 2 list of customers and perform a distinct to return unique customers.

```csharp
```

## LINQ GroupByDynamic Method

The LINQ GroupByDynamic method groups the elements of a sequence. You can use a dynamic string expression to specify columns.

In this example, we will select customers and group them dynamically by their countries.

```csharp
```

## LINQ OrderByDescendingDynamic Method

The LINQ OrderByDescendingDynamic method sorts the elements of a sequence in descending order. You can use a dynamic string expression to specify columns.

In this example, we will select customers and order them dynamically by their first names in descending order.

```csharp
```

## LINQ OrderByDynamic Method

The LINQ OrderByDynamic method sorts the elements of a sequence in ascending order. You can use a dynamic string expression to specify columns.

In this example, we will select customers and order them dynamically by their first names in ascending order.

```csharp
```

## LINQ ReserveDynamic Method

The LINQ ReserveDynamic method inverts the order of the elements in a sequence.

In this example, we will select customers and filter them dynamically by their countries. Then we will reverse the returned list order.

```csharp
```

## LINQ SelectDynamic Method

The LINQ SelectDynamic projects each element of a sequence into a new form. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select some customer properties, and returns a list.

```csharp
```

## LINQ SelectManyDynamic Method

The LINQ SelectManyDynamic projects each element of a sequence to an IEnumerable and flattens the resulting sequences into one sequence. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select some customer item properties, and returns a list.

```csharp
```

## LINQ SkipWhileDynamic Method

The LINQ SkipWhileDynamic method bypasses elements in a sequence as long as a specified condition is true and then returns the remaining elements. You can use a dynamic string expression to create the predicate.
 
In this example, we will select customers and skip the first 5 customers by filtering them dynamically by their countries.

```csharp
```

## LINQ TakeWhileDynamic Method

The LINQ TakeWhileDynamic method returns elements from a sequence as long as a specified condition is true, and then skips the remaining elements. You can use a dynamic string expression to create the predicate.

In this example, we will select customers and take the first 5 customers by filtering them dynamically by their countries.

```csharp
```

## LINQ ThenByDescendingDynamic Method

The LINQ ThenByDescendingDynamic method performs a subsequent ordering of the element in a sequence in descending order. You can use a dynamic string expression to specify columns in the sorting.

In this example, we will select customers and order them dynamically by their first names and then by their last names in descending order.

```csharp
```

## LINQ ThenByDynamic Method

The LINQ ThenByDynamic method performs a subsequent ordering of the element in a sequence in ascending order. You can use a dynamic string expression to specify columns in the sorting.

In this example, we will select customers and order them dynamically by their first names and then by their last names in ascending order.

```csharp
```

## LINQ WhereDynamic Method

The LINQ WhereDynamic method filters a sequence of values based on a predicate. You can use a dynamic string expression to create the predicate.

In this example, we will select customers and filter them dynamically by their countries.

```csharp
```

## LINQ AllDynamic Method

The LINQ AllDynamic method determines whether all elements of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select customers and check if all of them exist by filtering them dynamically by their countries.

```csharp
```

## LINQ AnyDynamic Method

The LINQ AnyDynamic method determines whether any element of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select customers and check if any of them exist by filtering them dynamically by their countries.

```csharp
```

## LINQ CountDynamic Method

The LINQ CountDynamic method returns the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.

In this example, we will select customers and returns the number of customer by filtering them dynamically by their countries.

```csharp
```

## LINQ ElementAtDynamic Method

The LINQ ElementAtDynamic method returns the element at a specified index in a sequence.

In this example, we will select customers, filter them dynamically by their countries, and return the second customers found (position 1).

```csharp
```

## LINQ FirstDynamic Method

The LINQ FirstDynamic returns the first element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select and return the first customer by dynamically filtering them for a specific email address.

```csharp
```

## LINQ FirstOrDefaultDynamic Method

The LINQ FirstOrDefaultDynamic returns the first element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select and return the first customer by dynamically filtering them for a specific email address.

```csharp
```

## LINQ LastDynamic Method

The LINQ LastDynamic returns the last element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select and return the last customer by dynamically filtering them for a specific email address.

```csharp
```

## LINQ LastOrDefaultDynamic Method

The LINQ LastOrDefaultDynamic returns the last element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select and return the last customer by dynamically filtering them for a specific email address.

```csharp
```

## LINQ LongCountDynamic Method

The LINQ LongCountDynamic method returns an Int64 that represents the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.

In this example, we will select customers and returns the number of customer by filtering them dynamically by their countries.

```csharp
```

## LINQ MaxDynamic Method

The LINQ MaxDynamic returns the maximum value in a sequence. You can use a dynamic string expression to select a specific column.

In this example, we will select customers and get the maximum number of orders a customer has made by choosing this column dynamically.

```csharp
```

## LINQ MinDynamic Method

The LINQ MinDynamic returns the minimum value in a sequence. You can use a dynamic string expression to select a specific column.

In this example, we will select customers and get the minimum number of orders a customer has made by choosing this column dynamically.

```csharp
```

## LINQ SelectToListDynamic Method

The LINQ SelectDynamic projects each element of a sequence into a new form and create a List from it. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select some customer properties, and returns a list.

```csharp
```

## LINQ SelectManyToListDynamic Method

The LINQ SelectManyDynamic projects each element of a sequence to an IEnumerable, and flattens the resulting sequences into one sequence and creates a List from it. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select some customer item properties, and returns a list.

```csharp
```

## LINQ SingleDynamic Method

The LINQ SingleDynamic returns a single, specific element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select and return a single customer by dynamically filtering them for a specific email address.

```csharp
```

## LINQ SingleOrDefaultDynamic Method

The LINQ SingleDynamic returns a single, specific element of a sequence, or a default value if that element is not found. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will select and return a single customer by dynamically filtering them for a specific email address.

```csharp
```

## LINQ SumDynamic Method

The LINQ SumDynamic returns the sum of a sequence of numeric values. You can use a dynamic string expression to select a specific column.

In this example, we will select customers and sum the number of orders they made by choosing this column dynamically.

```csharp
```

## LINQ ToArrayDynamic Method

The LINQ ToArrayDynamic creates an Array from an IEnumerable.

In this example, we will select customers, filter them dynamically by their countries, and return the result as an array.

```csharp
```

## LINQ ToListDynamic Method

The LINQ ToListDynamic creates a List from an IEnumerable.

In this example, we will select customers, filter them dynamically by their countries, and return the result as a list.

```csharp
```


