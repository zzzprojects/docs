---
Title: Getting Started with Dapper Plus in Under 10 Minutes
MetaDescription: Learn the basics of Dapper Plus in under 10 minutes. Install the library, execute bulk inserts, map entities, and explore options.
LastMod: 2024-09-18
---

# Getting Started with Dapper Plus

So, you're ready to try Dapper Plus for the first time?

In under 10 minutes, I promise that you'll already be comfortable using our library in your day-to-day programming.

You will learn:
- How easily you can use the [BulkInsert](/bulk-insert) and other [Bulk Extension Methods](/bulk-extensions-methods) to reduce your saving times by up to **99%**
- How to [map](/mapping) your entities to save them the way **you want**
- How to access [hundreds of options](/options) to customize your saving operations
- And some more!

Enough talk, let's get started!

## Download Dapper Plus

The first step is obviously to add the Dapper Plus NuGet package to your project.

You can find it either by visiting our [download](/download) page or by using this direct NuGet link: [https://www.nuget.org/packages/Z.Dapper.Plus](https://www.nuget.org/packages/Z.Dapper.Plus).

Even if you haven't purchased the library yet, you can fully try **all our features for free**. Every month, a new [trial](/trial) version is available that expires at the end of the month. Using the latest version allows you to test our library without having to purchase it.

Don’t forget to also leave us feedback to enter our [contest to win a free license](https://dapper-plus.net/contest).

## Using Directive

The second step, now that the library has been added to your project, is to include the `using Z.Dapper.Plus;` header directive in your file. This allows you to access all our extension methods. You add it just like you would with the `using Dapper` directive:

```csharp
using Dapper;
using Z.Dapper.Plus;
```

So far, so good. We've done exactly what you normally do when using [Dapper](https://www.learndapper.com/) (adding the NuGet package and the header directive), so nothing should be surprising.

## My First BulkInsert

For our getting started example, we will assume that you need to save a list of orders and their corresponding order items.

First, let's insert all our entities with our [BulkInsert](/bulk-insert) method:

```csharp
DapperPlusManager.Entity<Order>()
	.Identity(x => x.OrderID, true);
DapperPlusManager.Entity<OrderItem>()
	.Identity(x => x.OrderItemID, true);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(orders);
connection.BulkInsert(orders.SelectMany(x => x.Items));
```

[Online Examples](https://dotnetfiddle.net/0ijPFA)

Nothing special in our example besides that the `OrderID` identity value has been automatically propagated to their corresponding items. See the following article if you want to know more about [propagating the identity value](/identity-key-propagation).

Let's now make our code even simpler by using what we call [chaining methods](/bulk-extensions-methods#chaining-methods), employing the same principle we usually see with LINQ:

```csharp
DapperPlusManager.Entity<Order>()
	.Identity(x => x.OrderID, true);
DapperPlusManager.Entity<OrderItem>()
	.Identity(x => x.OrderItemID, true);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(orders).ThenBulkInsert(x => x.Items);
```

[Online Examples](https://dotnetfiddle.net/lbjr8C)

The code is now simpler to write and easier to read, making it **easier to maintain**.

The same result can be achieved through our **FREE** [Single Methods](/single-extensions-methods).

## Mapping and Mapping Key

So far so good, but what if you only want to insert a few properties?

Now is the time to introduce [Mapping](/mapping) to save your entity type exactly how **you want**.

In our example, we will mix a lot of mapping logic to demonstrate as much as possible:

For the Order:
- We will map only the `Number` property.
- We will map a constant value (`IsActive`).
- We will map a value from a client-side evaluation (`TotalAmount`).
- We will map a value from a database-side evaluation (`CreatedDate`).

For the OrderItem:
- We will map the `OrderID` directly from the parent.
- We will call the `AutoMap` to complete the mapping.

```alert-note
**AutoMap**: Once you start mapping, we cannot automatically use the auto-mapping logic anymore unless you explicitly call the `AutoMap` method as we have shown for the OrderItem.
```

Here is the full mapping:

```csharp
DapperPlusManager.Entity<Order>()
	.Identity(x => x.OrderID)
	.Map(x => new { x.Number })
	.MapValue(true, "IsActive")
	.Map(x => x.Items.Sum(y => y.Price), "TotalAmount")
	.MapWithOptions(x => x.CreatedDate, options => {
		options.FormulaInsert = "GETDATE()";
	});

DapperPlusManager.Entity<OrderItem>()
	.Identity(x => x.OrderItemID)
	.Map(x => x.Parent.OrderID, "OrderID")
	.AutoMap();
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(orders).ThenBulkInsert(x => x.Items);

FiddleHelper.WriteTable(connection.Query<Order>("SELECT * FROM [Order]"));
FiddleHelper.WriteTable(connection.Query<OrderItem>("SELECT * FROM [OrderItem]"));
```

To see it in action, try this [Online Example](https://dotnetfiddle.net/FQWqTt).

However, something may seem odd in this example, as you probably don't want this mapping to be your default behavior whenever you call [BulkInsert](/bulk-insert)!

Dapper Plus allows you to map your entity type an unlimited number of times by using a [Mapping Key](/mapping-key) and specifying it whenever you want to save your entity this way.

```csharp
DapperPlusManager.Entity<Order>("Order_CustomInsert")
	.Identity(x => x.OrderID)
	.Map(x => new { x.Number })
	.MapValue(true, "IsActive")
	.Map(x => x.Items.Sum(y => y.Price), "TotalAmount")
	.MapWithOptions(x => x.CreatedDate, options => {
		options.FormulaInsert = "GETDATE()";
	});

DapperPlusManager.Entity<OrderItem>("OrderItem_CustomInsert")
	.Identity(x => x.OrderItemID)
	.Map(x => x.Parent.OrderID, "OrderID")
	.AutoMap();
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert("Order_CustomInsert", orders).ThenBulkInsert("OrderItem_CustomInsert", x => x.Items);

FiddleHelper.WriteTable(connection.Query<Order>("SELECT * FROM [Order]"));
FiddleHelper.WriteTable(connection.Query<OrderItem>("SELECT * FROM [OrderItem]"));
```

[Online Example](https://dotnetfiddle.net/cnl9Vu)

Another technique would be to use a new instance of the [DapperPlusContext](dapper-plus-context).
 
## My First Options

It's finally time to explore some of the [hundreds of options](/options) available in Dapper Plus.

To use options, you need to employ the `UseBulkOptions` method from either the connection or the mapping, depending on the scenario.

For example, let's say I want to log all queries executed by Dapper Plus. Doing this from the connection would make more sense:

```csharp
connection.UseBulkOptions(options => { options.Log += s => Console.WriteLine(s); })
	.BulkInsert(orders).ThenBulkInsert(x => x.Items);
```

[Online Example](https://dotnetfiddle.net/hkCmZv)

While using the `UseBulkOptions` for options that are more related to the mapping, such as specifying that you want to keep the identity value when inserting, you can apply it with a [Mapping Key](/mapping-key):

```csharp
DapperPlusManager.Entity<Order>()
	.Identity(x => x.OrderID, true);

DapperPlusManager.Entity<OrderItem>()
	.Identity(x => x.OrderItemID, true);

DapperPlusManager.Entity<OrderItem>("OrderItem_KeepIdentityValue")
	.UseBulkOptions(options => { options.InsertKeepIdentity = true; });
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(orders).ThenBulkInsert("OrderItem_KeepIdentityValue", x => x.Items);
```

[Online Example](https://dotnetfiddle.net/Ijouh7)

## Conclusion

In this getting started tutorial, we have explored almost all of the most common usages of the Dapper Plus library.

As I initially promised, by now you should already be comfortable using our library in your day-to-day programming.

If you want to further master Dapper Plus and better understand how to utilize it effectively, you can continue by exploring all our [Bulk Extension Methods](#bulk-extensions-methods) and then all our **FREE** [Single Extension Methods](/single-extensions-methods).