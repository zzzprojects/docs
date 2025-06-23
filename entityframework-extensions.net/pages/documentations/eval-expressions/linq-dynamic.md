---
Name: LINQ Dynamic
LastMod: 2025-06-23
---

# LINQ Dynamic

## Description
You can execute query dynamically through the [Eval-Expression.NET](https://eval-expression.net/) library.

You can download it here: [Download](https://eval-expression.net/download)

> The Eval-Expression.NET library can be activated with the Entity Framework Extensions license. 

## LINQ Dynamic - Predicate
You can use any LINQ method that supports predicate with a dynamic C# expression:

- Deferred
   - OrderByDescendingDynamic
   - OrderByDynamic
   - SelectDynamic
   - SelectMany
   - SkipWhileDynamic
   - TakeWhileDynamic
   - ThenByDescendingDynamic
   - ThenByDynamic
   - WhereDynamic
- Immediate
   - AllDynamic
   - AnyDynamic
   - CountDynamic
   - FirstDynamic
   - FirstOrDefaultDynamic
   - LastDynamic
   - LastOrDefaultDynamic
   - LongCountDynamic
   - SingleDynamic
   - SingleOrDefaultDynamic

### Example
```csharp
var list = new List<int>() { 1, 2, 3, 4, 5 };

var list2 = list.WhereDynamic(x => "x > 2");
var list3 = list.WhereDynamic(x => "x > X", new { X = 2 }); // with parameter
```
{% include component-try-it.html href='https://dotnetfiddle.net/S42mkU' %}

### Example
```csharp
var list = new List<int>() { 5, 2, 4, 1, 3 };

// SelectDynamic
var list2 = list.SelectDynamic(x => "new { y = x + 1 }");
var list3 = list.SelectDynamic(x => "new { y = x + 1 }", new { y = 1 });

// OrderByDynamic
var list4 = list.OrderByDynamic(x => "x + 1");
var list5 = list.OrderByDynamic(x => "x + Y", new { Y = 1 });
```

## LINQ Dynamic - Execute
You can push the LINQ Dynamic experience further by using the Execute method and chaining anything else:

- Execute
- Execute&lt;TResult&gt;

### Example
```csharp
var list = new List<int>() { 1, 2, 3, 4, 5 };

var list2 = list.Execute<List<int>>("Where(x => x > 2).OrderBy(x => x).ToList()");
var list3 = list.Execute<List<int>>("Where(x => x > y).OrderBy(x => x).ToList()", new { y = 2 });
```
