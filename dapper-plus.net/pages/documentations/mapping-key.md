---
Title: Unlocking the Power of a Mapping Key: Map the Same Entity Type Multiple Time 
MetaDescription: Unlocking the Power of a Mapping Key: Map the Same Entity Type Multiple Time 
LastMod: 2024-06-27
---

# Unlocking the Power of a Mapping Key: Map the Same Entity Type Multiple Time 

A major problem with most moderm ORM like [EF Core](https://www.learnentityframeworkcore.com/) is once your map your Entity Type, it's mapped this way all the time and you cannot change it.

Sure it makes sense for the default behavior but the reality is another story. Sometime you want to:
- Insert or update only a few properties
- Use a custom key (an `ExternalID` when importing data for example)
- Insert data if it doesn't exists

Those are not the default behavior but are required from time to time. People using Entity Framework  usually choose to use library like our [Entity Framework Extensions](https://entityframework-extensions.net/) that solve this issue.


But for Dapper Plus, how do we solve this problem?

Dapper Plus solve this problem by allowing you to specify a custom mapping key:

```csharp
// TODO
```

In this [Online Example](#), we have 3 different mappings. One without a mapping key (usualy the default behavior), one with the `InsertIfNotExists` mapping key, and one with `InsertKeepIdentity` mapping key. As we can see, we pass to our [Bulk Insert](#) method the mapping key we want the method to use and only this mapping will be used to have different behavior for the same entity type.

Providing a mapping key is very useful for specifying different [Mapping](#) and [Options](#) for the same entity type as we learned from previous articles but will also help for some kind of [Data Source](#) as weill soon learn.

## When to use a mapping key

My preference is not using a mapping key for the default behavior. It make simply the code easier to use for 95% of my cases.

But whenever I have a different behavior then the default one, I automatically start to create a new mapping key that self describe if possible this behavior:

```csharp
// TODO
```

Weither you choose to always use one or not strickly depend on you and doesn't affect the performance.

## Benefits and Examples of Using the Dapper Plus Mapping Key

By now, you should understand what is a mapping key but now let explore some scenarios that make some sense.

Let take the scenario that I want a mapping for my update but also a mapping for a partial update only 2 specific columns:

```csharp
```

Let take the scenario that I have different database and each database require some special customization as the SQL for formula by example are diffrent:

```csharp
// Use formula
```

## Storing Mapping Key: Enum vs Hardcoded String 


In our previous example, we hardcoded the string as it easier to show.

However hardcoding your string might lead to error, especially if you change your string.

To solve it, we recommand you to use an enum instead and put ALL your mapping key in it instead. So whenever you change a name in your enum to fix a spelling mistake, you will be able to quickly change as well (or let visual studio do it with the rename option) everywhere in your solution.

```csharp
// TODO
```


## Conclusions 

We have seen in this article how powerful is a mapping key. This is one trick that a lot of people miss....

Now that we know what is mapping key and how to use it, it's now time to better understand about [Mapping & Modeling](#)