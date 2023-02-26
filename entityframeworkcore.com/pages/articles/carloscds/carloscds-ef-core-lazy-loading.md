---
Permalink: articles/carloscds-ef-core-lazy-loading
Title: Understanding Lazy Loading in EF Core
MetaDescription: Learn about lazy loading in Entity Framework Core and how it can improve performance when working with related data in your application.
tags: carloscds article lazy-loading proxy
OriginalLink: https://carloscds.net/2018/07/ef-core-lazy-loading/
CreatedDate: 2018-07-02
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
LastMod: 2023-02-20
---

# Exploring the Benefits and Limitations of Lazy Loading in EF Core

After a long time without writing about Entity Framework, I will resume with a series of articles about the new version: Entity Framework Core.

For those who still don't know, EF Core is part of the [.NET Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro), which is cross-platform.

First, we need to understand that EF Core is a new ORM, created literally from ZERO, and for this reason, it still has many things to do! Remember that [Entity Framework 6](https://github.com/dotnet/ef6) continues to exist as part of the .NET Full Framework.

In EF Core, with the release of version 2.1, we had a good evolution of the tool, and also the implementation of `Lazy Loading`.

## But what is Lazy Loading?

When we have relationships in our data model, for example, a `Customer` with `Order`, EF allows us, when reading the customer, that orders can be accessed as well without using `Include`.

But this can make everything very slow, as we can have several customers, with several orders, and the orders also have other relationships, such as products, sellers, etc.

For data loading to be faster, lazy loading  is employed and the related data is retrieved only if they are consulted, or triggered.

To demonstrate this in practice, let's create a new [Visual Studio Code project](https://code.visualstudio.com/) console project, and to make this even more fun, let's do everything on the command line:

```dotnet-console
dotnet new console
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

These commands will create a `.NET Core` console project and add the EF Core and SQL Server provider.

I will use the `Northwind` database for this example (I will put the script on Git)

Now that we have the project, let's create two classes: `Customer` and `Order` (If you prefer, you can reverse engineer using this other [article](/articles/carloscds-ef-core-power-tools)):

<script src="https://gist.github.com/carloscds/5231b7e3e0a0c761f532c14db673adfe.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/5231b7e3e0a0c761f532c14db673adfe">Gist</a>.</noscript>

<script src="https://gist.github.com/carloscds/0d29a804aaef7f42e44825d66ad5dcd7.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/0d29a804aaef7f42e44825d66ad5dcd7">Gist</a>.</noscript>

In this example, I am not using all columns in the tables, as this will not interfere.

And finally our context class:

<script src="https://gist.github.com/carloscds/5089b9da487dc6b3204b1ce2ea85e59f.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/5089b9da487dc6b3204b1ce2ea85e59f">Gist</a>.</noscript>

Well, so far nothing different, right? So let's list the data:

<script src="https://gist.github.com/carloscds/b1d42852493dd2f48e7fdaa8517247dd.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/b1d42852493dd2f48e7fdaa8517247dd">Gist</a>.</noscript>

And the result of the execution:

<img src="https://carloscds.net/wp-content/uploads/2018/07/2018-07-02_13-02-17-300x225.png" width="401" height="301" alt="LazyLoading-1"/>

See that all customers have ZERO Orders. That is because LazyLoading is still disabled!

So let's enable LazyLoading by adding a new package to our project:

```dotnet-console
dotnet add package Microsoft.EntityFrameworkCore.Proxies
```

We can now enable lazy loading in our context with the following code in the `Context` class:

```csharp
using Microsoft.EntityFrameworkCore.Proxies;
```

and by adding the command:

```csharp
optionsBuilder.UseLazyLoadingProxies();
```

Our class will look like this:

<script src="https://gist.github.com/carloscds/191a42fd6370b307798e5852e7f1db48.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/191a42fd6370b307798e5852e7f1db48">Gist</a>.</noscript>

Okay, now we will execute it again and we will have the Orders:

<img src="https://carloscds.net/wp-content/uploads/2018/07/2018-07-02_13-14-39-300x233.png" width="433" height="336" alt="LazyLoading-2"/>

If we compare it to EntityFramework 6, it was a little different, as we only have options for `Lazy Loading` and `Proxy`, which I don't find particularly interesting, since the resulting object comes with the proxy, see:

<img src="https://carloscds.net/wp-content/uploads/2018/07/2018-07-02_13-18-15-300x130.png" width="598" height="259" alt="LazyLoading-3"/>

That is because Microsoft used Castle Proxy to implement `Lazy Loading`, something that will be improved in later versions.

The source code for this project is on my GitHub at [https://github.com/carloscds/EFCoreSamples](https://github.com/carloscds/EFCoreSamples)