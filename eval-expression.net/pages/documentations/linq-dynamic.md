# LINQ Dynamic

## Description
(All LINQ Dynamic methods are FREE to use)

Extend the IEnumerable&lt;T&gt; and IQueryable&lt;T&gt; interface with methods to use LINQ with dynamic expressions.

Since LINQ (Language Integrated Query) has been introduced in .NET 3.5, it has been one of the most used feature. ORM like Entity Framework takes advantage of this feature and allows you to write type-safe queries.

In theory, you should only use it to write type-safe queries. However, the reality often requires you to write LINQ with dynamic query expressions.

## LINQ Dynamic - Predicate
You can use any LINQ method that supports predicate with a dynamic C# expression :

- Deferred
   - OrderByDescendingDynamic
   - OrderByDynamic
   - SelectDynamic
   - SelectMany
   - SkipWhile
   - TakeWhile 
   - ThenByDescendingDynamic
   - ThenByDynamic
   - Where
- Immediate
   - All
   - Any
   - Count
   - First
   - FirstOrDefault
   - Last
   - LastOrDefault
   - LongCount
   - Single
   - SingleOrDefault

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
