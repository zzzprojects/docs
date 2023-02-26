---
Permalink: articles/carloscds-ef-core-dependency-injection
Title: Dependency Injection with EF Core: In-Memory Database and Invoking Dependencies
MetaDescription: Learn how to use Entity Framework Core with an in-memory database and invoke injected dependencies without using class constructors. Follow our step-by-step guide and simplify your development process with Dependency Injection in EF Core.
tags: carloscds article dependency-injection inmemory
OriginalLink: https://carloscds.net/2020/02/injecao-de-depenencia-em-c-bonus/
CreatedDate: 2020-02-27
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
LastMod: 2023-02-20
---

# How to Use Dependency Injection with EF Core: In-Memory Database and Invoking Dependencies

In the last article in this series, I will show you two straightforward things: first, how to use the EntityFramework Core in memory and how to invoke an injected dependency without using the class constructor!

But why wouldn't you inject the dependency on the constructor, as I showed in the previous articles? Well, sometimes, it is necessary to use alternative mechanisms to simplify the development and have access to the objects!

## EntityFramework Core

For many years I wrote about EF, but as there was a long delay in the development of EF Core, I ended up using other ORMs in my projects. But now EF Core is fascinating, and I went back to using it on a massive project!

So, I decided to complement this series by showing an extremely useful feature of EF Core, which is the in-memory database.

But why so useful? Imagine that you develop using a "development" database or even local on your machine, which is perfectly normal. Still, over time this database can get "dirty," or with "bad" data, so we have to clean and start all over again.

Then think that you can keep an entire database in memory, performing the same operations that you would do in the typical database, but without saving anything anywhere except in the memory.

This feature is very valuable for unit tests, and I really hope you are writing tests!

_For this example we are going to create an ASP.NET Core WebAPI project._

## Creating the EF Core Context

The first thing we will do is create a class (table) and the context. 

We will have a simple `Client` class :

<script src="https://gist.github.com/carloscds/d224f094c8470ce181b87a291b967a5d.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/d224f094c8470ce181b87a291b967a5d">Gist</a>.</noscript>

Now let's get to the `Context`:

<script src="https://gist.github.com/carloscds/47565a0bd42ead503848fdf2ad8e81d4.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/47565a0bd42ead503848fdf2ad8e81d4">Gist</a>.</noscript>

Our context has nothing much, except a `SeedData()` method that I am using to fill the `Client` table in memory.

Remember that I am using a memory database, so to have data, I need to fill this database when I start the context!

## Configuring Startup.cs

Now let's configure the services in the Startup class:

<script src="https://gist.github.com/carloscds/583f0053d807d2597b2c81c3e352c8c5.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/583f0053d807d2597b2c81c3e352c8c5">Gist</a>.</noscript>

See that we are adding a context with the option `UseInMemoryDataBase()`

To use this method, you will need the following Entity packages:

```csharp
Microsoft.EntityFrameworkCore
Microsoft.EntityFrameworkCore.InMemory
```

The `InMemory` package allows you to have a database in memory. See that you can exchange `UseInMemoryDataBase()` for `UseSqlServer()` or any other database provider, and everything is still working!

When I write unit tests, I create an `InMemory()` context, and in the real application, I use a database! That makes my job much easier!

So we are injecting an Entity Framework context into our project through `AddDbContext()`, and also two other classes: `IHttpContextAccessor` and `IsendEmail` (`IEnvioEmail`), using Singleton and Scoped, which I showed in the <a href="https://carloscds.net/2020/02/injecao-de-dependencia-em-c-parte-2/" target="_blank">previous article<a/>.

`IHttpContextAccessor` allows us to access the ASP.NET Core Http Context anywhere it is injected, and `IEnvioEmail` is an example class that I created to simulate the sending of email.

See here the interface `IEnvioEmail` and the class `EnviarEmail` :

<script src="https://gist.github.com/carloscds/d5d758d937cf41c021a9abc81aa53ae2.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/d5d758d937cf41c021a9abc81aa53ae2">Gist</a>.</noscript>

<script src="https://gist.github.com/carloscds/046ded779d61c59c972898af046a7fc7.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/046ded779d61c59c972898af046a7fc7">Gist</a>.</noscript>

This class "simulates" sending an email, I imagine you will implement a real class for this!

## Working with Injection in the Controller

Now that we have our environment set up, let's create a controller to work with this data. We will create `ClientController.cs`, which will have two actions: `Get()` to bring all customers and `Email()` to "simulate" the sending of email.

Let's go to the code:

<script src="https://gist.github.com/carloscds/fe20c63aee0a89ba256a598fdacfbb6c.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/fe20c63aee0a89ba256a598fdacfbb6c">Gist</a>.</noscript>

Okay, we have a lot of things going on here, and I will explain:

In the controller constructor we are receiving some objects by injection:

- `ILogger logger` = ASP.NET Core log
- `AplicacaoContext db` = Entity Framework context
- `IHttpContextAccessor httpContextAccessor` = Http Context of the application

All are assigned to variables within the controller. So let's go to the methods that interest us.

The `Get()` method returns all database customers, which in our case are only in memory!

The other method, `Email()`, is the one that we are going to show the "invocation" of a dependency without the constructor.

## Understanding the scenario

That is more common than you think! In our project, we need to send an email, and the email class is in dependency injection (we set it up in `Startup.cs`), but we don't put it in the controller constructor because it's a little-used method, and in this case, it doesn't justify it hypothetically! Or it is simply injected into another class, which is even more common! In any case, we do not have this class directly available.

So, let's "materialize" the object, looking for it in the dependency injector, see how simple it is:

```csharp
var email = _httpContextAccessor.HttpContext.RequestServices.GetService<IEnvioEmail> ();
```

Let's understand:

`_httpContextAccessor` is the application's Http context, which has `HttpContext` and `Request`, which is the request for the page.

We use `RequestServices.GetServices<IEnvioEmail>()` which basically "asks" for the dependency injector to "give" us the object corresponding to the `IEnvioEmail` interface.

That is very cool because you are "injecting" an object by hand. You are taking an object that is already created and bringing it to the variable `email`.

And now, let use the email!

Running the application, we will have the following result:

<img src="https://carloscds.net/wp-content/uploads/2020/02/image-6.png" alt="RequestServices.GetServicesRequest">

Now calling the email:

<img src="https://carloscds.net/wp-content/uploads/2020/02/image-7.png" alt="RequestServices.GetServices<IEnvioEmail>">

## Conclusion

The dependency injection mechanism allows us to simplify development greatly! But use with care, as everything in excess is harmful!

Special thanks to my friend <a href="https://ralms.net/" target="_blank">Rafael Almeida</a>, with whom I "argue" frequently about EntityFramework!!! These discussions have already generated improvements in EF Core, such as `WithNoLock()`.

As always, the example code is on my <a href="https://github.com/carloscds/CSharpSamples/tree/master/InjecaoDependenciaDiretaEF" target="_blank">Github<a/>.

Hugs, and see you soon!