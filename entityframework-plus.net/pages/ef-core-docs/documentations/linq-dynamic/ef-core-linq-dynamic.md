---
Permalink: ef-core-linq-dynamic
---

# LINQ Dynamic

## Description

**LINQ Dynamic** in Entity Framework is supported through the [Eval-Expression.NET](https://eval-expression.net/linq-dynamic) Library. That feature is free to use even for commercial library.

## Predicate

All LINQ predicate methods are supported. A string expression which return a Boolean function can be used as parameter.

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

{% include template-example.html %} 
```csharp

var list = ctx.WhereDynamic(x => "x > 2").ToList();
var list = ctx.WhereDynamic(x => "x > y", new { y = 2 }).ToList();

```
[Try it](https://dotnetfiddle.net/1sTQwA)

{% include template-example.html %} 
```csharp

var list = context.Customers.OrderByDescendingDynamic(x => "x.Name").ToList();
var list = context.Customers.SelectDynamic(x => "x.Name").ToList();

```
[Try it](https://dotnetfiddle.net/8n2Xc0)

## Execute

The Execute method is the LINQ Dynamic ultimate methods which let you evaluate and execute a dynamic expression and return the result.

 - Execute
 - Execute< TResult >

{% include template-example.html %} 
```csharp
 
var list = ctx.Execute<IEnumerable<int>>("Where(x => x > 2)");
var list3 = ctx.Execute("Where(x => x > y).OrderBy(x => x).ToList()", new { y = 2 });

```
[Try it](https://dotnetfiddle.net/LbU2at)

