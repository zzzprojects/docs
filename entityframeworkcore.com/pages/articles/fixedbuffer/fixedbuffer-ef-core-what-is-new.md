---
Permalink: articles/fixedbuffer-ef-core-what-is-new
Title: Entity Framework Core 3.0: What's New?
MetaDescription: Entity Framework Core 3.0 brings many new features and performance improvements, including unique SQL statements, client evaluation restriction, C# 8 support, new tooling, and compliance with .NET Standard 2.1. This article explores some of the most important features and changes in design.
tags: fixedbuffer article release
OriginalLink: https://www.fixedbuffer.com/entity-framework-core-3-0-que-novedades-nos-trae/
CreatedDate: 2019-10-22
CreatedUserName: JorTurFer
CreatedUserLink: https://www.fixedbuffer.com/
LastMod: 2023-02-20
---

# A Look at the Most Important Features of Entity Framework Core 3.0

_Reading time: 4 minutes_

<img src="https://www.fixedbuffer.com/wp-content/uploads/2018/09/EFCore.png" alt="The picture shows the Entity Framework Core logo">

A few weeks ago, since version 3.0 of .Net Core was released, and it has brought us many new features: C # 8, expanded support in Windows, performance improvements, [Worker Services](https://www.fixedbuffer.com/worker-service-como-crear-un-servicio-net-core-3-multiplataforma/) ... And of course, it also brings us a new version of Entity Framework Core.

There are many new features that this new release brings us, which provide us with performance improvements, but also changes in design. In this post, we are going to see some of the most important features of this new version, although I recommend that you take a look at the [complete list](https://docs.microsoft.com/ef/core/what-is-new/ef-core-3.0/) of changes that Entity Framework Core 3.0 brings (and also the list of [breaking changes](https://docs.microsoft.com/ef/core/what-is-new/ef-core-3.0/breaking-changes)).

## Unique SQL statement per LINQ query

The SQL statement generation engine has been improved, previously, queries that we made with several tables, could lead to executing several queries to the database instead of a single one (which is what we expect when using it). Entity Framework Core 3.0 guarantees that only a single query will be generated, and in case it cannot be translated into a single query, it will throw an exception that will indicate it to us so that we can refactor the code.

## Restriction of client evaluation

In previous versions, if a query could not be translated into SQL, what was known as a client evaluation was performed. That means that a query with the maximum possible filters applied was going to be executed in the database, and those that cannot be translated were applied on client-side itself at the cost of fetching the raw data and processing it in our application. Imagine this code (obtained from the official documentation):

```csharp
public static string StandardizeUrl(string url)
{
    url = url.ToLower();
    if (!url.StartsWith("http://"))
    {
        url = string.Concat("http://", url);
    }
    return url;
}

var blogs = context.Blogs
    .Where(blog => blog.Date > DateTime.Now.AddYears(-1) && StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

We are using a condition that the database cannot execute so that the behavior will be:

1. We make a query that returns the entire table filtered by date.
2. We filter the whole table in memory to apply our custom condition.
3. We generate a list with filtered results

Although this can sometimes be useful, if the table gets large, it will be a great loss of performance. By default, in previous versions, querying was allowed, and a warning was indicated. **This default behavior has changed in Entity Framework Core 3.0, now it will throw an exception that will indicate it to us**. If we want to do the client evaluation, we have to do it explicitly:

```csharp
var blogs = context.Blogs
    .Where(blog => blog.Date > DateTime.Now.AddYears(-1))
    .AsEnumerable() // Explicitely tell that the query is not longer queryable
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

## C # 8 support

The new version of C # brings us many improvements and new APIs, including [IAsyncEnumerable](https://docs.microsoft.com/en-gb/dotnet/api/system.collections.generic.iasyncenumerable-1?view=netcore-3.1), which together with `await foreach`, will allow us to improve the speed at which queries are executed, since we are going to process the data as they become available. Thanks to C # in Entity Framework Core 3.0, we can do something like this:

```csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

## Entity Framework Core 3.0 tooling is no longer part of .NET Core 3.0

A major design change is that .NET Core no longer has a built-in toolset for working with the Entity Framework Core. In other words, we are going to need to install the toolkit separately if we are really interested in using it. We can do this locally with the manifest file or globally with the command:

```dotnet-console
dotnet tool install --global dotnet-ef
```

## It is developed for .Net Standard 2.1

This new version of Entity Framework Core, is developed complying with .NET Standard 2.1, this is what allows it to have great performance improvements. Still, it limits the platforms where it can be used, **.NET Core 3.0 is compatible with .NET Standard 2.1, but .NET Framework is not supported**, so if you need to use it in .NET Framework, you will not be able to use Entity Framework Core 3.0.

## Conclusion

The truth is that the changes offered by this new version are not limited to this. I wanted to highlight those that, in my opinion, are the most important or necessary to take into account if you come from working with previous versions. Still, I highly recommend that you take a look at the new [features](https://docs.microsoft.com/ef/core/what-is-new/ef-core-3.0/) and above all, the [list of changes that do not they are backward compatible](https://docs.microsoft.com/ef/core/what-is-new/ef-core-3.0/breaking-changes)  . You will see that this is a very interesting new version with many improvements.