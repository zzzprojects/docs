---
Permalink: query-cache
CanonicalLink: https://entityframework-plus.net/ef-core-query-cache
Name: Query Cache
---

# Query Cache

## Description

Caching entities or query results to improve an application's performance is a very frequent scenario. Major ORMs like NHibernate had this feature for a long time but, unfortunately for Entity Framework users, second-level caching is only available through third-party libraries.

Caching is very simple to understand, the first time a query is invoked, data is retrieved from the database and stored in memory before being returned. All future calls will retrieve data from memory to avoid making additional database round trips which drastically increases an application's performance.

**EF+ Query Cache** opens up all caching features for Entity Framework users.

To use caching, simply append to the query "FromCache" method before using an immediate resolution method like "ToList()" or "FirstOrDefault()".

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// The first call performs a database round trip
var countries1 = ctx.Countries.FromCache().ToList();

// Subsequent calls will take the value from memory instead
var countries2 = ctx.Countries.FromCache().ToList();

```
[Try it](https://dotnetfiddle.net/nx9A2H)

## Scenarios

 - [Query Criteria](#query-criteria)
 - [Query Deferred](#query-deferred)
 - [Query Primitive Value](#query-primitive-value)
 - [Tag & ExpireTag](#tag-expiretag)
 - [Expiration](#expiration)
 - [Query Cache Control](#query-cache-control)
 
### Query Criteria

Return the query result from the cache. If the query is not cached yet, it will be materialized and cached before being returned.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// The query is cached using default QueryCacheManager options
var countries = ctx.Countries.Where(x => x.IsActive).FromCache();

```
[Try it](https://dotnetfiddle.net/z5gyPI)

[Try it (Async version)](https://dotnetfiddle.net/U9pU1a)

### Query Deferred

Immediate resolution methods like **Count()** and **FirstOrDefault()** cannot be cached since their expected behavior is to cache the count result or only the first item.

{% include template-example.html %} 
```csharp

// Oops! The query is already executed, we cannot cache it.
var count = ctx.Customers.Count();

// Oops! All customers are cached instead of customer count.
var count = ctx.Customers.FromCache().Count();
```
[Try it](https://dotnetfiddle.net/3EXZBt)

**EF+ Query Deferred** has been created to resolve this issue, the resolution is now deferred instead of being immediate which lets us cache the expected result.

{% include template-example.html %} 
```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// The count is deferred, it is now cached.
var count = ctx.Customers.DeferredCount().FromCache();

```
[Try it](https://dotnetfiddle.net/V0G6pe)

Query Deferred supports all queryable extension methods and overloads.

### Query Primitive Value

A primitive value such as `int` and `double` cannot be cached as the method requires a reference type.

To cache a primitive value, you can use an anonymous type such as:

{% include template-example.html %} 
```csharp
var customers = context.Customers.Select(x => new { x.CustomerID }).FromCache();
```
[Try it](https://dotnetfiddle.net/rYl0Lm)

### Tag & ExpireTag

Tagging the cache lets you expire later on all cached entries with a specific tag by calling the **ExpireTag** method.

For example, the daily countries & states importation has been completed and you need to refresh from the database all queries related to the country table. You can now simply expire the tag **countries** to remove all related cached entries.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// Cache queries with related tags
var states = ctx.States.FromCache("countries", "states");
var stateCount = ctx.States.DeferredCount().FromCache("countries", "states", "stats");

// Expire all cache entries using the "countries" tag
QueryCacheManager.ExpireTag("countries");
:bulb: Use Enum instead of hard-coding string for tag name.

```
[Try it](https://dotnetfiddle.net/PUQCCY)

### Expiration

All common caching features like absolute expiration, sliding expiration, removal callback are supported.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// Make the query expire after 2 hours of inactivity

var options = new MemoryCacheEntryOptions() { SlidingExpiration = TimeSpan.FromHours(2)};

var states = ctx.States.FromCache(options);

```
[Try it](https://dotnetfiddle.net/aKPnTD)

### Query Cache Control

EF+ Cache is very flexible and lets you have full control over the cache.

You can use your own cache:

{% include template-example.html %} 
```csharp
QueryCacheManager.Cache = new MemoryCache(new MemoryCacheOptions());
context.Customers.FromCache().ToList();

```
[Try it](https://dotnetfiddle.net/6ISVBT)

You can set default policy

{% include template-example.html %} 
```csharp

var options = new MemoryCacheEntryOptions() { SlidingExpiration = TimeSpan.FromHours(2)};
QueryCacheManager.DefaultMemoryCacheEntryOptions = options;
context.Customers.FromCache().ToList();

```
[Try it](https://dotnetfiddle.net/k1TOWX)
 
## Real Life Scenarios

 - Caching read-only data like application configuration.
 - Caching data that can only be modified via an import like states & countries and make all related cache entries expire once the import is completed (Tag Expiration).
 - Caching data that are frequently accessed but rarely modified like comments and expire the tag when a new comment is added or after one hour without any access (Tag Expiration & Sliding Expiration).
 - Caching statistics that don't require to be live like client count and expire them every hour (Absolute Expiration).

## Behind the code

When **FromCache** is invoked, the QueryCacheManager returns the result if a cache entry exists for the cache key. If no cache entry exists, the query is materialized then cached in memory using the cache key before being returned. If a cache tag is specified, the cache key is also stored in a concurrent dictionary for all tags.

 - **The Cache:** The memory cache is used by default.

 - **The Cache Key:** The cache key is created by combining a cache prefix, all cache tags, and the query expression.

 - **The Query Materialized:** The query is materialized by either using "ToList()" method or "Execute()" method for query deferred.

## Conclusion

As we have seen, EF+ Query Cache follows a good architectural principle:

 - **Flexible:** Tag & Cache Options make it possible to use Query Cache in a variety of scenarios.
 - **Extensible:** If there is something missing, the library creates your own extension method or your own cache to overcome the problem.
 - **Maintainable:** The easy to use API, documentation and available source code allows new developers to quickly understand this feature.
 - **Scalable:** Caching gets only better as the number of users/traffic grows by drastically reducing database round trips.

Need help getting started? [info@zzzprojects.com](mailto:info@zzzprojects.com)

We welcome all comments, ideas and suggestions to improve our library.
