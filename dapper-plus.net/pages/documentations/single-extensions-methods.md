---
Title: The Easiest Way to Save Data: Single Extension Methods (100% Free) 
MetaDescription: The Easiest Way to Save Data: Single Extension Methods (100% Free) 
LastMod: 2024-06-27
---

TBD: Add also a context part???

# The Easiest Way to Save Data: Single Extension Methods (100% Free)

In our getting started with our [Bulk Extension Methods](#) article, we saw that one of the major advantage is that you don't have to write your saving SQL for every entity type. They are usually boring to write and follow pretty much the same pattern. Who like to write them anyway?

Surely not me! But then if you only have a few entities to save, would not be nice to still enjoy this major advantage but for free?

Surely we agree! **Single methods are 100% free and doesn't require any license.** So feel free to use them for your personal projects, commercial projects, or any other kind of projects.

Let me repeat, they are free to use!

Single methods offer your the same flexibility and same advantages as their bulk extensions equivalent. However, you trade off the high performance advantage for the **100% free** advantage.

You can use any of those single method with one entity at a time:
- [SingleInsert](#): This method allow you to **INSERT** one row
- [SingleUpdate](#): This method allow your to **UPDATE** one row
- [SingleDelete](#): This method allow your to **DELETE** one row
- [SingleMerge](#): This method is an **UPSERT** operation. It will **UPDATE** if the row exists, or **INSERT** if not exists.

Here is our example from our first [Getting Started](#) article but using single method this time:
```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
		
// Easy to use
connection.SingleInsert(customer);

// Easy to customize
connection.UseBulkOptions(options => options.InsertIfNotExists = true)
          .SingleInsert(customer);
```

Again, the code speak by itself as you already know what they will do:

- The first `BulkInsert` will insert one customer in your database 
- The second `BulkInsert` will insert one customer if it doesn't already exists.

## How to Use Single Action Methods With Multiple Rows?

The answer is simple: You cannot. **Oh WAIT! Yes you can**, you just have to use the `foreach` statement!

If we take our main example that we have see in our [Bulk Extension Methods](#) article, we can easily do it this way:

```csharp
Merge Customer
Insert Order
Insert OrderItem
```

The main differense is one database round-trip will be required for every action executed. So if you have only a few entities to save, single method are great. But if you have hundred or thousand entitiy to save, we recommand you to use bulk extension method instead.

## Async Method

All our single extension methods offer their async equivalence:

- [SingleInsertAsync](#)
- [SingleUpdateAsync](#)
- [SingleDeleteAsync](#)
- [SingleMergeAsync](#)

Let take our previous example that insert/merge multiple entities using the `ForEach` statement but this time asynchrousnouly:

```csharp
// TODO
```

And as we learned from our previous [Bulk Extensions Async](#) article, you can pass the `CancellationToken` from the connection:

```csharp
// TODO
```

## Conclusion

This getting started article introduced Single Extension Methods. By now, you should already know:

- They are **100% free** to use
- You can insert multiple rows but without the performance [Bulk Extension Methods](#) provide
- You can use them asynchrously

Since they are free, why not trying them right now by [downloading Dapper Plus](#)?

It's now the time to continue our getting started journey by learning how to map your entities and customize your saving operation through options we offer. But first, let understand [What is the Dapper Plus Context](#) which is a prerequiste for this.