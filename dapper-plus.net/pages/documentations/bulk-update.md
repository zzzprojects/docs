---
Title: Bulk Update | How to Quickly Update Multiple Rows in Dapper
MetaDescription: Learn how to perform faster update in Dapper using the Bulk Update method, understand why it's essential, and explore some common scenarios.
LastMod: 2024-09-21
---

# Bulk Update: How to Quickly Update Multiple Rows in Dapper

The Dapper Plus `BulkUpdate` extension method enables you to quickly update multiple rows in your database. This method is up to **75x faster** than conventional Dapper update techniques and offers extensive customization with [hundreds of options](/options).

```csharp
// Easy to use
connection.BulkUpdate(orders);

// Easy to customize
connection.UseBulkOptions(options => options.InsertIfNotExists = true)
          .BulkUpdate(orders);
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

In this article, we will explore why using the `BulkUpdate` method can be extremely beneficial for your projects. We will also discuss some common options and scenarios, such as conditional updating (only updating rows if at least one value is different).

## Benchmark

The traditional technique for updating multiple rows in Dapper requires you to write your `UPDATE` statement and pass a list of entities to the [execute](https://www.learndapper.com/non-query) method:

```csharp
// TODO
connection.Execute(sql, anonymousCustomers);
```

**The problem** is similar to what we have seen in our [Bulk Insert Benchmark](/bulk-insert#benchmark); one database round-trip is required for every row that needs to be updated, making the entire update operation significantly slower than if you use the Dapper Plus `BulkUpdate` method.

Let's compare the performance of both techniques:

| Technique         | 50 Entities | 2,000 Entities | 5,000 Entities |
| :---------------- | -----------:| --------------:| --------------:|
| Update (Execute)  | 1,200 ms    | 2,400 ms       | 6,000 ms       |
| BulkUpdate        | 50 ms       | 55 ms          | 75 ms          |

You can directly try this [online benchmark](https://dotnetfiddle.net/CqTwfr) in your browser.

Not only does `BulkUpdate` offer significantly better performance, but the code is also clearer and easier to maintain.

## Getting Started with Bulk Update

To get started, you should read the [Bulk Extensions Methods](/bulk-extensions-methods) documentation as the concept is the same for all our bulk methods. Here is a recap:

- You can update asynchronously with the `BulkUpdateAsync` method.
- You can [chain](/bulk-extensions-methods#chaining) operations with the `AlsoBulkUpdate` and `ThenBulkUpdate` methods.
- You can use `BulkUpdate` from a connection, transaction, or a new [Dapper Plus Context](/dapper-plus-context).
- You can utilize the `BulkUpdate` method with multiple different [DataSources](/datasource).

```csharp
// Example code will be provided here
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

## Common Options / Scenarios

In this section, we will explore some common options and scenarios that developers often use with the `BulkUpdate` method:

- Custom Key
- Conditional Update
- Coalesce Update

For more options, refer to our [list of options](/options) documentation.

### Custom Key

A common scenario when using `BulkUpdate` involves updating a large number of entities coming from an external data source. The entities you want to import may not be aware of the internal key of your system, only the external key.

In Dapper Plus, you can map your entities multiple times in different ways using a [mapping key](/mapping#mapping-key).

```csharp
// Example code demonstrating Custom Key mapping
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

### Conditional Update

Why update a row if no column values have been modified? This is often a requirement reported by our customers.

You can choose to update only if at least one column has a different value using the `UpdateMatchedAndOneNotConditionExpression` and `UpdateMatchedAndOneNotConditionNames` options:

```csharp
// Example code demonstrating Conditional Update
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

### Coalesce Update

Handling null values is another common scenario reported to us. If your database column value is not null but your entity's property value is null, you can opt to keep the database value with the `CoalesceDestinationOnUpdateExpression` and `CoalesceDestinationOnUpdateNames` options:

```csharp
// Example code demonstrating Coalesce Update
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

## Conclusion

In this documentation, we introduced the `BulkUpdate` method. Its ease of use, coupled with [hundreds of different options](/options), makes this method particularly valuable. While manually coding multiple options could be challenging and prone to human error, using these options directly through Dapper Plus allows for faster coding and ensures that each feature has been thoroughly tested.