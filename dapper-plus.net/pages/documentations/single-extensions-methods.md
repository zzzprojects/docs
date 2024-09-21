---
Title: The Easiest Way to Save Data: Single Extension Methods (100% Free) 
MetaDescription: The Easiest Way to Save Data: Single Extension Methods (100% Free) 
LastMod: 2024-09-21
---

# The Easiest Way to Save Data: Single Extension Methods (100% Free)

In our [Bulk Extension Methods](/bulk-extensions-methods) documentation, we highlighted a significant advantage: you don't have to write your SQL statements for saving entities. These SQL statements are often repetitive and tedious to write. Honestly, who enjoys that?

Certainly not me! But what if you only have a few entities to save? Wouldn't it be great to still enjoy this major advantages of the bulk extension methods, but for free?

We thought you'd agree! **Single methods are 100% free and require no license.** Feel free to use them in your personal projects, commercial projects, or any other type of project.

To reiterate, they are free to use!

Single methods offer the same flexibility and advantages as their bulk counterparts, except they trade off the **high performance** of bulk operations for being **100% free**.

You can use any of these single methods with one entity at a time:
- **SingleInsert:** Allows you to **INSERT** one row.
- **SingleUpdate:** Allows you to **UPDATE** one row.
- **SingleDelete:** Allows you to **DELETE** one row.
- **SingleMerge:** Performs an **UPSERT** operation; **UPDATE** if the row exists, **INSERT** if it does not.

Here is an example from our initial [overview](/overview) documentation, now using a single method:

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
		
// Easy to use
connection.SingleInsert(products[0]);

// Easy to customize
connection.UseBulkOptions(options => options.InsertIfNotExists = true)
		  .BulkInsert(products[1]);
```

[Online Example](https://dotnetfiddle.net/pwcR2q)

Again, the code speaks for itself, as you already know what it will do:

- The first `SingleInsert` will insert one product into your database.
- The second `SingleInsert` will insert one product only if they don't already exist.

## Single Insert

The `SingleInsert` and `SingleInsertAsync` methods allow you to **INSERT** one row.

For more information about usage and customization, refer to the equivalent [BulkInsert](/bulk-insert) methods.

## Single Update

The `SingleUpdate` and `SingleUpdateAsync` methods allow you to **UPDATE** one row.

For more details on usage and customization, see the equivalent [BulkUpdate](/bulk-update) methods.

## Single Delete

The `SingleDelete` and `SingleDeleteAsync` methods allow you to **DELETE** one row.

For additional information on usage and customization, refer to the equivalent [BulkDelete](/bulk-delete) methods.

## Single Merge

The `SingleMerge` and `SingleMergeAsync` methods perform an **UPSERT** operation. They will **UPDATE** if the row exists or **INSERT** if it does not.

For more documentation about usage and customization, see the equivalent [BulkMerge](/bulk-merge) methods.

## Single Synchronize

The `SingleSynchronize` and `SingleSynchronizeAsync` methods are equals to a **MIRROR** operation; they ensure your table matches your data source exactly. This includes **UPDATING** existing rows, **INSERTING** non-existing rows, and **DELETING** rows that are not in your data source. However, this method is rarely practical as it mirrors only one row.

For more details on usage and customization, refer to the equivalent [BulkSynchronize](/bulk-synchronize) methods.

## How to Use Single Action Methods With Multiple Rows?

The answer is simple: You cannot. **Oh WAIT! Yes you can**, but you'll need to use the `foreach` statement!

Taking our initial example, you can apply the single methods in this way:

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

// Easy to use
products.ForEach(x => connection.SingleInsert(x));

// Easy to customize
products.ForEach(x => connection.UseBulkOptions(options => options.InsertIfNotExists = true)
		  .BulkInsert(x));
```

[Online Example](https://dotnetfiddle.net/f0Hf1F)

The main difference is that one database round-trip will be required for every action executed. So, if you have only a few entities to save, single methods are great as the performance impact is minimal. However, if you need to save hundreds or thousands of entities, we recommend using the [bulk extensions methods](/bulk-extensions-methods) instead for better efficiency and performance.

## Conclusion

In this documentation, we introduced our single extension methods. Here are the key points to remember:

- They are **100% free** to use.
- You can insert multiple rows using a `foreach` loop, but without the performance benefits that [Bulk Extension Methods](/bulk-extensions-methods) offer.
- These methods are available for asynchronous operations.

Since they are free, why not try them out right now by [downloading Dapper Plus](/downloads)?