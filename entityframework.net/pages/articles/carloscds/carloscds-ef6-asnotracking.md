---
Permalink: articles/carloscds-ef6-asnotracking
Title: How to Use AsNoTracking in Entity Framework to Avoid Unintentional Changes
MetaDescription: Learn how to use AsNoTracking in Entity Framework to avoid unintentional changes and improve performance. See code samples and examples in this article.
LastMod: 2023-02-24
tags: carloscds article asnotracking change-tracker
OriginalLink: https://carloscds.net/2018/12/entityframework-asnotracking-por-que-preciso-saber-disto/
CreatedDate: 2018-02-12
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# How to Use AsNoTracking in Entity Framework to Avoid Unintentional Changes

This week I came across a problem with a client that is very common and causes a lot of drawbacks since it involves various concepts of how EF works.

When you create a context for EF and indicate the mapping classes, you basically tell it that all objects must be tracked. The simple fact that you create an object or read from the context puts this object in the `ChangeTracker`.

## Let's consider the following context:

<script src="https://gist.github.com/carloscds/21e3031092a6b09aa0421ec8beb88907.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/21e3031092a6b09aa0421ec8beb88907">Gist</a>.</noscript>

Now let's simulate a dependency injection, where we have a `Data` (Dados) class that receives the context:

<script src="https://gist.github.com/carloscds/89f79b8892dcc291fe9155a1cd218ddb.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/89f79b8892dcc291fe9155a1cd218ddb">Gist</a>.</noscript>

Let's remember that the basic principle of dependency injection is the propagation of the object. In this case, we have only one context, which is used by all classes through the injection mechanism!

Now let's consult the database and bring a category:

<script src="https://gist.github.com/carloscds/f8307cf42a1067450c57ff9031bec3b2.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/f8307cf42a1067450c57ff9031bec3b2">Gist</a>.</noscript>

From this moment on, the `cat` object is part of the EF mapping. It will track this object, control the state of this object (`Added`, `Modified`, `Deleted`, etc.). Well, so far so good, because that's exactly what we hope he does.

The problem starts when we instantiate other objects, remembering that we are in a dependency injection environment. We will then perform a new query and we will also list the objects that are on the EF tracker:

<script src="https://gist.github.com/carloscds/865d10eca6a9e7090cd9c6ed7a4c7f62.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/865d10eca6a9e7090cd9c6ed7a4c7f62">Gist</a>.</noscript>

Once the code is executed, we have the following result:

```csharp
Beverages
System.Data.Entity.DynamicProxies.Categories_58C84246D9EE9DEB30950140620833728474B6132D2BC59BD4306359B33CE2A1, Modified
System.Data.Entity.DynamicProxies.Categories_58C84246D9EE9DEB30950140620833728474B6132D2BC59BD4306359B33CE2A1, Unchanged
That indicates that EF is mapping the two objects, even though they have been consulted in different instances of the Data class, as they share the same context.
```

That indicates that EF is mapping the two objects, even though they have been consulted in different instances of the data class, as they share the same context.

Therefore, if we call a `SaveChanges()` command and we have changed the two objects, even "unintentionally", they will be sent to the database.

## And how do we solve this?

There are several ways to solve it, and perhaps the first one that comes to your mind is "let's instantiate another context", but if I do this, what is the benefit of dependency injection in this case?

For this type of situation, we have the `AsNoTracking()` method, which in very simple terms, tells the context not to track the object.

So let's create another `GetNoTracking()` method by implementing this call:

<script src="https://gist.github.com/carloscds/78401114238446cce2347c7a0b8da8d0.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/78401114238446cce2347c7a0b8da8d0">Gist</a>.</noscript>

See that we only add `AsNoTracking()` in the query!

Now we will change the call to the first object, which in this example is read-only, we will not modify anything in it:

<script src="https://gist.github.com/carloscds/5d0a62e9607d60d83e3b963b6a767ca1.js"></script><noscript>View the code on <a href="https://gist.github.com/carloscds/5d0a62e9607d60d83e3b963b6a767ca1">Gist</a>.</noscript>

Running the code another time will give different result  since only one object is assigned to EF:

```csharp
Beverages
System.Data.Entity.DynamicProxies.Categories_58C84246D9EE9DEB30950140620833728474B6132D2BC59BD4306359B33CE2A1, Modified
```

## Conclusion

If you are just querying an object in EF that you are not going to modify and save it, use `AsNoTracking()` whenever possible.

That does not mean that you cannot instantiate another context, but avoiding this can give you a performance gain!

The source code is on my GitHub: [https://github.com/carloscds/CSharpSamples/tree/master/EFAsNoTracking](https://github.com/carloscds/CSharpSamples/tree/master/EFAsNoTracking)