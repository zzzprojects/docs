---
Title: What is Dapper Plus? Join Thousands of Companies Boosting Performance Worldwide
MetaDescription: Learn what Dapper Plus is, who uses this library, why you need this library, how it helps you to develop faster, and how Dapper Plus helps Dapper.
LastMod: 2024-09-17
---

# Getting Started with Dapper Plus

So you are ready to try Dapper Plus for the first time?

Under 10 minutes, I promise that you already will be confortable using our library in your day to day basis.

You will learn:
- How easily you can use the [BulkInsert](/bulk-insert) method to reduce your saving times by up **99%**
- How to [map](/mapping) your entities to save it the way **you want**
- How to have access to [hundred of options](/options)

Enough talk, let get started!

## Download Dapper Plus

The first step is obviously adding the Dapper Plus NuGet package to your project.

You can find it by either go on our [download](/download) page or by using directly this NuGet link: [https://www.nuget.org/packages/Z.Dapper.Plus](https://www.nuget.org/packages/Z.Dapper.Plus)

Even if you didn't have purchased the library yet, you can fully try all our features for free. Every month, a new [trial](/trial) is available, that expires at the end of the month. So using the latest version allow you to test our library without having to purchase it.

Don't forget also to leave us a feedback to enter in our [contest to win a free license](https://dapper-plus.net/contest)

## Using Directive

The second step now that the library has been added to your project is adding the `using Z.Dapper.Plus;` header directive to your file, so you can have access to all our extension methods.

```csharp
using Dapper
using Z.Dapper.Plus
```

So far, so good we did exactly the same as you normally do when using [Dapper](https://www.learndapper.com/) (Adding the NuGet package and the header directive), so nothing should be suprising.

## My First BulkInsert / SingleInsert

For our getting started example, we will assume that we have to save a list of order and their corresponding order items.

First, let insert our entities with our [BulkInsert](/bulk-insert) method:

```csharp
```

[Online Examples](#)

Nothing special in our example beside that we have to propagate the `OrderID` identity values to the order items. See the following article if you want to know more about [propagating the identity value](/identity-key-propagation)

Let now make our code even simpler by using what we call [chaining methods](/bulk-extensions-methods#chaining-methods), this is the same principe of what we usally do through LINQ:

```csharp
```

[Online Examples](#)

The code is now simpler to write and simpler to read making it easier to maintains. On this example, we used the `ThenForEach` method to propagate the identity value this time.

For people that want to use Dapper Plus for FREE, you can achieve the same result by using [SingleInsert](/single-extensions-methods) extension method:

```csharp

```

[Online Examples](#)

## Mapping and Mapping Key

So far so good, but what if you only want to insert a few number of properties?

This is now time to introduce the [Mapping](/mapping) to save your entity type the way **you want**. In our example, we will mixe a lot of mapping logic to show as much as we can:

For the Order:
- We will map only a few properties
- We will map a constant value
- We will map a value from a client-side evaluation
- We will map a value from a database side evaluation

For the OrderItem:
- We will map the `OrderID` directly from the parent
- We will ignore a few properties
- We will call the `AutoMap` to complete the mapping

```alert-note
**AutoMap**: Once you start to map, we cannot automatically use the automapping logic anymore unless you call explicitely the `AutoMap` method as we have shown for tyhe OrderItem.
```

Here is the full example:

```csharp
TODO
```

[Online Example](#)

However something remain weird in this example, as you probably don't want this mapping to be your default behavior whenever you call the [BulkInsert](/bulk-insert)!

Dapper Plus allow you to map your entity time an unlimited amount of time by using a [Mapping Key](/mapping-key) and specify it whenever you want to save your entity.

```csharp
TODO
```

[Online Example](#)

Another technique would have to use a new instance of the [DapperPlusContext](dapper-plus-context).
 
## My First Options

It's finally time to see how we can use some of the [hundreds of options](/options) available in Dapper Plus.

To use options, you need to use the `UseBulkOptions` method from the connection or the mapping depending of the scenario.

By example, let say I would like to log all query executed from Dapper Plus, doing it from the connection would make more sense:

```csharp
```

[Online Example](#)

While using the `UseBulkOptions` such as options that are more related to the mapping such as use the `UseTableLock`, or etc...

```csharp
```

[Online Example](#)

## Data Source

We have seen that Dapper Plus is flexible by allowing to provide a lot of options but also for supporting multiple kind of [Data Source](/data-source).

In this example, we will assume that the data was imported from a CSV and loaded in a DataTable.

```csharp
```

## Conclusion

In this getting started tutorial, we have seen almost all the most common usage of the Dapper Plus library.

As I intiialy promised, by now you should already be confortable using our library in your day to day basis.

If you want to already master Dapper Plus better understand how to utilize it effectively, then you can continue by exploring all our [Bulk Extensions Methods](#bulk-extensions-methods) and then all our **FREE** [Single Extensions Methods](/single-extensions-methods).