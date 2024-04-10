---
Name: LINQ Dynamic
LastMod: 2024-04-10
---

# How to use LINQ with Dynamic Expression

Dynamically building a LINQ by adding a `Where` clause or `OrderBy` clause coming from user input is a very common scenario.

LINQ is a great addition to .NET, and is one of the most used features for ORM, such as Entity Framework, but it lacks the flexibility to create a dynamic query.

To overcome that limitation, there are **2 FREE** common solutions (both from [ZZZ Projects](https://zzzprojects.com/)):

- C# Eval Expression (This library!)
- [Dynamic LINQ](https://dynamic-linq.net/)

Most of the time, the C# Eval Expression is the best solution for using LINQ Dynamic:

- Using LINQ Dynamic method is free (you don't have to purchase a license)
- Use the C# Language
- Doesn't "interpret" the expression, unlike other libraries, instead it parses the expression and calls the underlying LINQ method
- Easy to pass parameters via anonymous type or dictionary
- Easy to know when a `Dynamic` method is used (they have all the same suffix!)

Let me repeat one more time one of his advantages: **The C# Eval Expression is free** when using LINQ Dynamic method.

Otherwise, another great alternative is using Dynamic LINQ which is also free. It makes it very easy to customize your query through a string. One major advantage and disadvantage at the same time is it doesn't use a C# syntax but instead its homemade syntax. If you want to learn more about this library, we recommend reading his [documentation](https://dynamic-linq.net/overview)


## Parameters

Before going deeper into the subject, let's see how parameters can be passed through a LINQ Dynamic method. Like [Execute Methods](/eval-execute), you can pass parameter throughs:

- Using an Anonymous Type
- Using a Class Instance
- Using a Dictionary<string, TValue>
- Using an Expando Object
- Using parameter values

## Method

Let's see first all LINQ methods available:

| Category  | Name                     | Description                                                                                                                                                                                                                             |
| --------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Misc.      | Execute                  | The LINQ Execute method lets you use a dynamic string expression to execute any LINQ methods. That is our most powerful LINQ method.                                                                                                                                     |
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
| Deferred  | WhereDynamic             | The LINQ WhereDynamic method filters a sequence of values based on a predicate. You can use a dynamic string expression to create the predicate.                                                                                       |
| Immediate | AllDynamic               | The LINQ AllDynamic method determines whether all elements of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                |
| Immediate | AnyDynamic               | The LINQ AnyDynamic method determines whether any element of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                 |
| Immediate | CountDynamic             | The LINQ CountDynamic method returns the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.                                                          |
| Immediate | ElementAtDynamic         | The LINQ ElementAtDynamic method returns the element at a specified index in a sequence.                                                                                                                                                |
| Immediate | FirstDynamic             | The LINQ FirstDynamic returns the first element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                                  |
| Immediate | FirstOrDefaultDynamic    | The LINQ FirstOrDefaultDynamic returns the first element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.                              |
| Immediate | LastDynamic              | The LINQ LastDynamic returns the last element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                                    |
| Immediate | LastOrDefaultDynamic     | The LINQ LastOrDefaultDynamic returns the last element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.                               |
| Immediate | LongCountDynamic         | The LINQ LongCountDynamic method returns an Int64 that represents the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.                             |
| Immediate | MaxDynamic               | The LINQ MaxDynamic returns the maximum value in a sequence. You can use a dynamic string expression to select a specific column.                                                                                                       |
| Immediate | MinDynamic               | The LINQ MinDynamic returns the minimum value in a sequence. You can use a dynamic string expression to select a specific column.                                                                                                       |
| Immediate | SelectToListDynamic      | The LINQ SelectToListDynamic projects each element of a sequence into a new form and create a List from it. You can use a dynamic string expression to select a specific column                                                              |
| Immediate | SelectManyToListDynamic  | The LINQ SelectManyToListDynamic projects each element of a sequence to an IEnumerable, and flattens the resulting sequences into one sequence and creates a List from it. You can use a dynamic string expression to select a specific column. |
| Immediate | SingleDynamic            | The LINQ SingleDynamic returns a single, specific element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.                                                                        |
| Immediate | SingleOrDefaultDynamic   | The LINQ SingleDynamic returns a single, specific element of a sequence, or a default value if that element is not found. You can use a dynamic string expression to filter elements that satisfy the condition.                       |
| Immediate | SumDynamic               | The LINQ SumDynamic returns the sum of a sequence of numeric values. You can use a dynamic string expression to select a specific column.                                                                                              |
| Immediate | ToArrayDynamic           | The LINQ ToArrayDynamic creates an Array from an IEnumerable.                                                                                                                                                                            |
| Immediate | ToListDynamic            | The LINQ ToListDynamic creates a List from an IEnumerable.                                                                                                                                                                               |                                                                                                                                                                           |

## LINQ Execute Method

The LINQ Execute method lets you use a dynamic string expression to execute any LINQ methods.

That is the most powerful LINQ method, as it allows you to chain multiple LINQ expressions one after another one.

Keep in mind that you currently execute C# code, so you should call directly LINQ method (`Where`) and not LINQ dynamic method (`WhereDynamic`).

In this example, we will select a list of product and dynamically perform a `Where` clause by their value, then an Order By and return a List.

```csharp
var list = new List<int>() { 1, 2, 3, 4, 5 };

var list2 = list.Execute<List<int>>("Where(x => x > 2).OrderBy(x => x).ToList()");
var list3 = list.Execute<List<int>>("Where(x => x > y).OrderBy(x => x).ToList()", new { y = 2 });
```

{% include component-try-it.html href='https://dotnetfiddle.net/MCIf2W' %}

## LINQ DistinctDynamic Method

The LINQ DistinctDynamic method returns distinct elements from a sequence.

In this example, we will combine 2 list of products and perform a distinct to return an unique list of products.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/" };

var list1 = new List<Product>() { product1, product2, product3 };
var list2 = new List<Product>() { product1 };

var combinedList = list1.Concat(list2);
var distinctList = list1.DistinctDynamic();
```

{% include component-try-it.html href='https://dotnetfiddle.net/df0dMW' %}

## LINQ GroupByDynamic Method

The LINQ GroupByDynamic method groups the elements of a sequence. You can use a dynamic string expression to specify columns.

In this example, we will create a list of product and group them by their type using a dynamic expression and select only the first group.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };
var firstGroup = list.GroupByDynamic("x => x.Type").Execute<List<Product>>(".First().ToList()");
```

{% include component-try-it.html href='https://dotnetfiddle.net/woUcsl' %}

## LINQ OrderByDescendingDynamic Method

The LINQ OrderByDescendingDynamic method sorts the elements of a sequence in descending order. You can use a dynamic string expression to specify columns.

In this example, we will create a list of product and order them dynamically by their name in descending order.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };
var orderByDescendingList = list.OrderByDescendingDynamic("x => x.Name").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/XbDe3v' %}

## LINQ OrderByDynamic Method

The LINQ OrderByDynamic method sorts the elements of a sequence in ascending order. You can use a dynamic string expression to specify columns.

In this example, we will create a list of product and order them dynamically by their name in ascending order.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };
var orderByList = list.OrderByDynamic("x => x.Name").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/I5C2Xp' %}

## LINQ ReserveDynamic Method

The LINQ ReserveDynamic method inverts the order of the elements in a sequence.

In this example, we will create a list of product and then we will reverse the list order.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };

list.ReverseDynamic();
```

{% include component-try-it.html href='https://dotnetfiddle.net/6zCrl4' %}

## LINQ SelectDynamic Method

The LINQ SelectDynamic projects each element of a sequence into a new form. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select some product properties, and returns an anonymous list.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };
var anonymousList = list.SelectDynamic("x => new { x.Name, x.Url }").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/LNZLhc' %}

## LINQ SelectManyDynamic Method

The LINQ SelectManyDynamic projects each element of a sequence to an IEnumerable and flattens the resulting sequences into one sequence. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select products from different catalog, and returns the full products list.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/" };

var catalog1 = new Catalog() { Type = "Paid", Products = new List<Product>() { product1, product2, product3 }};
var catalog2 = new Catalog() { Type = "Free", Products = new List<Product>() { product4, product5, product6 }};

var catalogs = new List<Catalog>() { catalog1, catalog2 };

var products = catalogs.SelectManyDynamic("x => x.Products").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/iS7OvU' %}

## LINQ SkipWhileDynamic Method

The LINQ SkipWhileDynamic method bypasses elements in a sequence as long as a specified condition is true and then returns the remaining elements. You can use a dynamic string expression to create the predicate.
 
In this example, we will create a list of product and ordering them descending per type and skipping all `paid` products (instead of using a `where` clause).

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };
var skipWhileList = list.OrderByDescendingDynamic("x => x.Type").SkipWhileDynamic("x => x.Type == 'Paid'").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/WOZBkM' %}

## LINQ TakeWhileDynamic Method

The LINQ TakeWhileDynamic method returns elements from a sequence as long as a specified condition is true, and then skips the remaining elements. You can use a dynamic string expression to create the predicate.

In this example, we will create a list of product and ordering them descending per type and taking all `paid` products (instead of using a `where` clause).

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };
var takeWhileList = list.OrderByDescendingDynamic("x => x.Type").TakeWhileDynamic("x => x.Type == 'Paid'").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/QaFJZh' %}

## LINQ ThenByDescendingDynamic Method

The LINQ ThenByDescendingDynamic method performs a subsequent ordering of the element in a sequence in descending order. You can use a dynamic string expression to specify columns in the sorting.

In this example, we will create a list of product and order them dynamically by type and then by their name in descending order.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };
var thenByDescendingList = list.OrderByDynamic("x => x.Type").ThenByDescendingDynamic("x => x.Name").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/J6GZAg' %}

## LINQ ThenByDynamic Method

The LINQ ThenByDynamic method performs a subsequent ordering of the element in a sequence in ascending order. You can use a dynamic string expression to specify columns in the sorting.

In this example, we will create a list of product and order them dynamically by type and then by their name in ascending order.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };
var thenByList = list.OrderByDynamic("x => x.Type").ThenByDynamic("x => x.Name").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/GYQm3m' %}

## LINQ WhereDynamic Method

The LINQ WhereDynamic method filters a sequence of values based on a predicate. You can use a dynamic string expression to create the predicate.

In this example, we will create a list of product and filter them by their type.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var whereList1 = list.WhereDynamic("x => x.Type == 'Paid'").ToList();
var whereList2 = list.WhereDynamic("x => x.Type == type", new { type = "Paid" }).ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/2o5lZJ' %}

## LINQ AllDynamic Method

The LINQ AllDynamic method determines whether all elements of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and check if they are all paid products.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var allBool1 = list.AllDynamic("x => x.Type == 'Paid'");
var allBool2 = list.AllDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/zG1CCg' %}

## LINQ AnyDynamic Method

The LINQ AnyDynamic method determines whether any element of a sequence exists. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and check if they are any paid products.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var anyBool1 = list.AnyDynamic("x => x.Type == 'Paid'");
var anyBool2 = list.AnyDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/HtIqVF' %}

## LINQ CountDynamic Method

The LINQ CountDynamic method returns the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.

In this example, we will create a list of product and count the number of paid products.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var count1 = list.CountDynamic("x => x.Type == 'Paid'");
var count2 = list.CountDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/57iHBa' %}

## LINQ ElementAtDynamic Method

The LINQ ElementAtDynamic method returns the element at a specified index in a sequence.

In this example, we will create a list of product and filter them dynamically by their type, and return the second product found (position 1).

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var elementAt = list.WhereDynamic("x => x.Type == 'Paid'").ElementAtDynamic(1);
```

{% include component-try-it.html href='https://dotnetfiddle.net/2DBp39' %}

## LINQ FirstDynamic Method

The LINQ FirstDynamic returns the first element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and return the first product by dynamically filtering them with their type.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var first1 = list.FirstDynamic("x => x.Type == 'Paid'");
var first2 = list.FirstDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/V0dblq' %}

## LINQ FirstOrDefaultDynamic Method

The LINQ FirstOrDefaultDynamic returns the first element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and return the first product or null by dynamically filtering them with their type.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var firstOrDefault1 = list.FirstOrDefaultDynamic("x => x.Type == 'Paid'");
var firstOrDefault2 = list.FirstOrDefaultDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/Y39z4R' %}

## LINQ LastDynamic Method

The LINQ LastDynamic returns the last element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and return the last product by dynamically filtering them with their type.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var last1 = list.LastDynamic("x => x.Type == 'Paid'");
var last2 = list.LastDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/hoI2oh' %}

## LINQ LastOrDefaultDynamic Method

The LINQ LastOrDefaultDynamic returns the last element of a sequence, or a default value if no element is found. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and return the last product or null by dynamically filtering them with their type.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var lastOrDefault1 = list.LastOrDefaultDynamic("x => x.Type == 'Paid'");
var lastOrDefault2 = list.LastOrDefaultDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/Ble03X' %}

## LINQ LongCountDynamic Method

The LINQ LongCountDynamic method returns an Int64 that represents the number of elements in a sequence. You can use a dynamic string expression to count the number of elements that satisfy the condition.

In this example, we will create a list of product and count the number of paid products.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var longCount1 = list.LongCountDynamic("x => x.Type == 'Paid'");
var longCount2 = list.LongCountDynamic("x => x.Type == type", new { type = "Paid" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/ztmW3J' %}

## LINQ MaxDynamic Method

The LINQ MaxDynamic returns the maximum value in a sequence. You can use a dynamic string expression to select a specific column.

In this example, we will create a list of product and get the product name considered as the maximum.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };

var max = list.MaxDynamic<Product, string>("x => x.Name");
```

{% include component-try-it.html href='https://dotnetfiddle.net/mN7s5k' %}

## LINQ MinDynamic Method

The LINQ MinDynamic returns the minimum value in a sequence. You can use a dynamic string expression to select a specific column.

In this example, we will create a list of product and get the product name considered as the minimum.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };

var min = list.MinDynamic<Product, string>("x => x.Name");
```

{% include component-try-it.html href='https://dotnetfiddle.net/dxcbcl' %}

## LINQ SelectToListDynamic Method

The LINQ SelectToListDynamic projects each element of a sequence into a new form and create a List from it. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select some product properties, cast our list as queryable (a queryable is required for this method), and returns an anonymous list.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };
var anonymousList = list.AsQueryable().SelectToListDynamic("x => new { x.Name, x.Url }");
```

{% include component-try-it.html href='https://dotnetfiddle.net/nt697k' %}

## LINQ SelectManyToListDynamic Method

The LINQ SelectManyToListDynamic projects each element of a sequence to an IEnumerable, and flattens the resulting sequences into one sequence and creates a List from it. You can use a dynamic string expression to select a specific column.

In this example, we will dynamically select products from different catalog, cast our list as queryable (a queryable is required for this method), and returns the full products list.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/" };

var catalog1 = new Catalog() { Type = "Paid", Products = new List<Product>() { product1, product2, product3 }};
var catalog2 = new Catalog() { Type = "Free", Products = new List<Product>() { product4, product5, product6 }};

var catalogs = new List<Catalog>() { catalog1, catalog2 };

var products = catalogs.AsQueryable().SelectManyToListDynamic("x => x.Products").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/GR5Ps3' %}

## LINQ SingleDynamic Method

The LINQ SingleDynamic returns a single, specific element of a sequence. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and return the last product by dynamically filtering them with a name.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var single1 = list.SingleDynamic("x => x.Name == 'C# Eval Expression'");
var single2 = list.SingleDynamic("x => x.Name == name", new { name = "C# Eval Expression" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/KkC0AK' %}

## LINQ SingleOrDefaultDynamic Method

The LINQ SingleDynamic returns a single, specific element of a sequence, or a default value if that element is not found. You can use a dynamic string expression to filter elements that satisfy the condition.

In this example, we will create a list of product and return the last product by dynamically filtering them with a name.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var singleOrDefault1 = list.SingleOrDefaultDynamic("x => x.Name == 'C# Eval Expression'");
var singleOrDefault2 = list.SingleOrDefaultDynamic("x => x.Name == name", new { name = "C# Eval Expression" });
```

{% include component-try-it.html href='https://dotnetfiddle.net/9laBHI' %}

## LINQ SumDynamic Method

The LINQ SumDynamic returns the sum of a sequence of numeric values. You can use a dynamic string expression to select a specific column.

In this example, we will create a list of product and sum the length of all product name characters.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };

var list = new List<Product>() { product1, product2, product3 };

var sum = list.SumDynamic<Product, int>("x => x.Name.Length");
```

{% include component-try-it.html href='https://dotnetfiddle.net/njJ46j' %}

## LINQ ToArrayDynamic Method

The LINQ ToArrayDynamic creates an Array from an IEnumerable.

In this example, we will create a list of product, filter them dynamically by their type, and return the result as an array.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var toArray = list.WhereDynamic("x => x.Type == 'Paid'").ToArrayDynamic();
```

{% include component-try-it.html href='https://dotnetfiddle.net/C10REg' %}

## LINQ ToListDynamic Method

The LINQ ToListDynamic creates a List from an IEnumerable.

In this example, we will create a list of product, filter them dynamically by their type, and return the result as a list.

```csharp
var product1 = new Product() { Name = "C# Eval Expression", Url = "https://eval-expression.net/", Type = "Paid" };
var product2 = new Product() { Name = "Dapper Plus", Url = "https://dapper-plus.net/", Type = "Paid" };
var product3 = new Product() { Name = "Entity Framework Extensions", Url = "https://entityframework-extensions.net/", Type = "Paid" };
var product4 = new Product() { Name = ".NET Fiddle", Url = "https://dotnetfiddle.net/", Type = "Free" };
var product5 = new Product() { Name = "SQL Fiddle", Url = "https://sqlfiddle.com/", Type = "Free" };
var product6 = new Product() { Name = "ZZZ Code AI", Url = "https://zzzcode.ai/", Type = "Free" };

var list = new List<Product>() { product1, product2, product3, product4, product5, product6 };

var toList = list.WhereDynamic("x => x.Type == 'Paid'").ToListDynamic();
```

{% include component-try-it.html href='https://dotnetfiddle.net/UO9Ocd' %}

