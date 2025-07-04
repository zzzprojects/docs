---
Permalink: ef-core-query-deferred
Name: Query Deferred
---

# Query Deferred

## Description

There are two types of IQueryable extension methods:

Deferred Methods: The query expression is modified but the query is not resolved (Select, Where, etc.).
Immediate Methods: The query expression is modified and the query is resolved (Count, First, etc.).
However, some third-party features like **Query Cache** and **Query Future** cannot be used directly with Immediate Methods since the query is already resolved.

**EF+ Query Deferred** provides more flexibility to other features.

{% include template-example.html %} 
```csharp

// Oops! The query is already executed, we cannot cache it.
var count = ctx.Customers.Count();

// Oops! All customers are cached instead of the customer count.
var count = ctx.Customers.FromCache().Count();

```
[Try it](https://dotnetfiddle.net/cu3UiE)

Here comes into play the deferred query which acts exactly like deferred methods, by modifying the query expression without resolving it.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// The count is deferred and cached.
var count = ctx.Customers.DeferredCount().FromCache();

```
[Try it](https://dotnetfiddle.net/xIz5wx)

#### All LINQ IQueryable extension methods and overloads are supported:

 - DeferredAggregate
 - DeferredAll
 - DeferredAny
 - DeferredAverage
 - DeferredContains
 - DeferredCount
 - DeferredElementAt
 - DeferredElementAtOrDefault
 - DeferredFirst
 - DeferredFirstOrDefault
 - DeferredLast
 - DeferredLastOrDefault
 - DeferredLongCount
 - DeferredMax
 - DeferredMin
 - DeferredSequenceEqual
 - DeferredSingle
 - DeferredSingleOrDefault
 - DeferredSum

## Options

 - [Using Query Cache and Query Future](#using-query-cache-and-query-future)
 - [Execute](#execute)
 
### Using Query Cache and Query Future

Defer the execution of a query which is normally executed to allow some features like Query Cache and Query Future.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// Query Cache
ctx.Customers.DeferredCount().FromCache();

// Query Future
ctx.Customers.DeferredCount().FutureValue();

```
[Try it](https://dotnetfiddle.net/ohLJL3)

### Execute

Execute the deferred query and return the result.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

var countDeferred = ctx.Customers.DeferredCount();
var count = countDeferred.Execute();

```
[Try it](https://dotnetfiddle.net/Ou2Ly4)

[Try it (Async version)](https://dotnetfiddle.net/1pttmj)

## Real Life Scenarios

EF Query Deferred brings advantages to other third-party features:

 - Allows using Immediate Methods with EF+ Query Cache.
 - Allows using Immediate Methods with EF+ Query Future.
 - Allows using Immediate Methods with YOUR own features.

## Behind the code
When a deferred method is used, the query expression is created exactly like a non-deferred method but instead of invoking the execute method from the query provider, a new instance of a class QueryDeferred<TResult> is created using the query and the expression.

The QueryDeferred instance has methods to either execute the expression from the query provider or let a third-party library use the object query.

## Conclusion

As we saw, EF+ **Query Deferred** brings considerable advantages to other libraries by letting them use immediate methods without removing any of their features.

Need help getting started? [info@zzzprojects.com](mailto:info@zzzprojects.com)

We welcome all comments, ideas and suggestions to improve our library.
