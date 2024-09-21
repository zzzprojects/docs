---
Title: Unlocking the Power of a Mapping Key: Map the Same Entity Type Multiple Time 
MetaDescription: Unlocking the Power of a Mapping Key: Map the Same Entity Type Multiple Time 
LastMod: 2024-06-27
---

# Unlocking the Power of a Mapping Key: Map the Same Entity Type Multiple Times

A major problem with most modern ORMs like [EF Core](https://www.learnentityframeworkcore.com/) is that once you map your Entity Type, it's mapped that way all the time and you cannot change it.

Sure, it makes sense for the default behavior, but the reality is another story. Sometimes you want to:
- Insert or update only a few properties
- Use a custom key (an `ExternalID` when importing data, for example)
- Insert data only if it doesn't exist

These are not the default behaviors but are required from time to time. People using Entity Framework usually choose to use a library like our [Entity Framework Extensions](https://entityframework-extensions.net/) that solves this issue.

But for Dapper Plus, how do we solve this problem?

Dapper Plus solves this problem by allowing you to map an entity type an unlimited number of times by specifying a **mapping key**:

```csharp
// Map your entity
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID);

DapperPlusManager.Entity<Product>("InsertIfNotExists")
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => x.InsertIfNotExists = true);

DapperPlusManager.Entity<Product>("InsertKeepIdentity")
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => x.InsertKeepIdentity = true);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert("InsertKeepIdentity", products);
```

In this [Online Example](https://dotnetfiddle.net/LRl5nt), we have three different mappings:

- One without a mapping key (usually the default behavior)
- One with the `InsertIfNotExists` mapping key
- One with the `InsertKeepIdentity` mapping key.

As we can see, we pass to our [Bulk Insert](/bulk-insert) method the mapping key we want the method to use, and only this mapping will be used to have different behavior for the same entity type.

Providing a mapping key is very useful for specifying different [Mapping](/mapping) and [Options](/options) for the same entity type, as we learned from previous articles, but will also help for some kinds of [Data Source](/data-source) like mapping a DataTable.

## When to Use a Mapping Key

My preference is not to use a mapping key for the default behavior. This simply makes the code easier to use for 95% of my cases.

But whenever I have a behavior different from the default one, I automatically start to create a new mapping key that, if possible, self-describes the behavior:

```csharp
DapperPlusManager.Entity<Product>("InsertKeepIdentity")
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => x.InsertKeepIdentity = true);
```

Whether you choose to always use a mapping key or not strictly depends on you and does not affect performance.

## Storing Mapping Keys: Variable/Enum vs. Hardcoded String

In our previous examples, the mapping key was directly a hardcoded string as it's easier to show.

However, hardcoding your strings might lead to errors, especially if you decide to change your string value.

To solve this, we recommend using static variables or an enum instead and placing ALL your mapping keys in it. This way, whenever you change a name in your enum to fix a spelling mistake or simply because you find a better name, you will be able to quickly change it as well, or let Visual Studio do it with the rename option everywhere in your solution.

```csharp
public static class DapperPlusMappingKey
{
	public static string InsertIfNotExists = "InsertIfNotExists";
	public static string InsertKeepIdentity = "InsertKeepIdentity";		
}
	
DapperPlusManager.Entity<Product>(DapperPlusMappingKey.InsertKeepIdentity)
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => x.InsertKeepIdentity = true);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(DapperPlusMappingKey.InsertKeepIdentity, products);
```

[Online Example](https://dotnetfiddle.net/hMOZO1)

## Conclusion

We have seen in this article how powerful a mapping key can be. Make sure to use it whenever you encounter a special scenario. It is often preferable to have a static mapping with a mapping key than to create a new instance of the [Dapper Plus Context](dapper-plus-context).