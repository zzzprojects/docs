---
Title: The Quickest Way to Save Data: Mastering Bulk Extension Methods
MetaDescription: Learn how to save 75x faster with Dapper Plus bulk extension methods, master bulk chaining methods, and use asynchronous bulk methods.
LastMod: 2024-09-09
---

# The Quickest Way to Save Data: Mastering Bulk Extension Methods

We have seen in our [Benchmark](https://dotnetfiddle.net/kz4UpX) from our getting started article that Dapper Plus was **75x faster** than the traditional way to [insert multiple rows with Dapper](https://www.learndapper.com/saving-data/insert#dapper-insert-multiple-rows), reducing saving time by **99%**.

Our library does more than only inserting; in fact, it covers all scenarios you might have:

- [BulkInsert](/bulk-insert): This method allows you to **INSERT** multiple rows in bulk.
- [BulkUpdate](/bulk-update): This method allows you to **UPDATE** multiple rows in bulk.
- [BulkDelete](/bulk-delete): This method allows you to **DELETE** multiple rows in bulk.
- [BulkMerge](/bulk-merge): This method is an **UPSERT** operation. It will **UPDATE** existing rows and **INSERT** non-existing rows in bulk.
- [BulkSynchronize](/bulk-synchronize): This method is like a **MIRROR** operation; your table becomes exactly like your datasource. In other words, it will **UPDATE** existing rows, **INSERT** non-existing rows, and **DELETE** rows that are not in your datasource.

Our library not only inserts very fast, but you will also see similar performance gains for deleting, updating, and merging (upsert). Additionally, you'll be able to **code more efficiently**.

Let's now see how simple it is to use these bulk extension methods. Let's assume we receive data from an external system that provides a list of customers with their new orders and order items.

- Since customers might already exist, we will use the [BulkMerge](/bulk-merge) method.
- Since orders and order items are always new, we will use the [BulkInsert](/bulk-insert) method.

```csharp
connection.BulkMerge("KeepIdentity", newCustomerOrders);
connection.BulkInsert(newCustomerOrders.SelectMany(x => x.Orders));
connection.BulkInsert(newCustomerOrders.SelectMany(x => x.Orders.SelectMany(y => y.Items)));
```

You can try it yourself in this [Online Example](https://dotnetfiddle.net/nd7sh3).

It was easy, no? Well, it will get even easier in the next section when we will learn about chaining methods.

## Chaining Methods

Chaining methods are a way to write code that flows. Over time, they become easier to write, read, and maintain.

We offer 4 types of chaining methods:

- **AlsoBulk[Action]:** Performs additional bulk actions at the last hierarchy level without changing the hierarchy level.
- **ThenBulk[Action]):** Performs additional bulk actions at the last hierarchy level and moves deeper into the hierarchy level.
- **ThenForEach:** Perform an action for each entity, such as [propagating the identity value](identity-key-propagation).
- **Include:** Performs included bulk actions at the last hierarchy level without changing the hierarchy level.

**Uhhh What?** Yeah, I guess those descriptions are hard to understand at first glance, but rest assured, you will easily understand and know how to use them once you finish this getting started section.

Let's go step by step by starting with only the [AlsoBulkInsert](#) method with the same example from our first one:

```csharp
connection.BulkMerge("KeepIdentity", newCustomerOrders)
		  .AlsoBulkInsert(customer => customer.Orders)
		  .AlsoBulkInsert(customer => customer.Orders.SelectMany(y => y.Items));
```

See the [Online Example](https://dotnetfiddle.net/IbHIlP)

As you can see, we only have to use the connection once, and all other methods are chained from it. The `AlsoBulk[Action]` method (in this case, `AlsoBulkInsert`) doesn't change the hierarchy level, so we always had to start from the customer.

However, the `OrderItem` is more related to the `Order` (the next hierarchy level), so starting from the customer for this one doesn't really make sense and makes the code harder to read.

This is now the time to introduce the [ThenBulkInsert](#) method to understand how to move the hierarchy level:

```csharp
connection.BulkMerge("KeepIdentity", newCustomerOrders)
		  .ThenBulkInsert(customer => customer.Orders)
		  .ThenBulkInsert(order => order.Items);
```

See the [Online Example](https://dotnetfiddle.net/Jhrw02)

We now see that the syntax for inserting `OrderItem` is much simpler and more readable.

Let's complicate our scenario a bit and assume we also need to insert a list of `Invoice` and `InvoiceItem` from the customer level. The problem with the previous example is that using `ThenBulkInsert` moved the hierarchy to the `Order` level, so how do we go back? Well, you can't! Those two methods alone cannot cover all scenarios as sometimes you need to return to the previous hierarchy level (the customer in this case).

So, it's time to introduce the `Include` method to fix this problem:

```csharp
connection.BulkMerge("KeepIdentity", newCustomerOrders)
		  .Include(c => c.ThenBulkInsert(customer => customer.Orders)
						 .AlsoBulkInsert(order => order.Items))
		  .Include(c => c.ThenBulkInsert(customer => customer.Invoices)
						 .AlsoBulkInsert(invoice => invoice.Items));	
```

See the [Online Example](https://dotnetfiddle.net/hTTWb0)

The `Include` method allows everything within it to move the hierarchy level but only affects what is inside the method. The `Include` method itself is not impacted and always stays at the same level (the customer).

Now that you have seen some explanations about how to implement them, read again the descriptions provided at the beginning of this section to see if they make more sense.

Once you know how to use them, you simply know how to use them. Whether you choose to use chaining methods or not will not impact performance. However, chaining bulk extension methods can surely help you to **write code faster**, make it **more readable**, and **easier to maintain**.

## Async Methods

All our bulk extension methods offer their asynchronous equivalents:

- [BulkInsertAsync](#)
- [BulkUpdateAsync](#)
- [BulkDeleteAsync](#)
- [BulkMergeAsync](#)
- [BulkSynchronizeAsync](#)

You can also chain methods as we previously saw, always awaiting the last bulk operation to be completed:

```csharp
await(await(await connection.BulkMergeAsync("KeepIdentity", newCustomerOrders))
		  .ThenBulkInsertAsync(customer => customer.Orders))
		  .AlsoBulkInsertAsync(order => order.Items);
```

See the [Online Example](https://dotnetfiddle.net/suurab)

What about the cancellation token? To make it simpler (for you and for us), we chose not to support it directly within the method itself but by allowing you to pass it using the `UseBulkOptions` method:

```csharp
await(await(await connection.UseBulkOptions(x => x.CancellationToken = cancellationToken).BulkMergeAsync(customers)
   .ThenBulkInsertAsync(customer => customer.Orders))
   .AlsoBulkInsertAsync(order => order.Items));
```

See the [Online Example](https://dotnetfiddle.net/AVG5eB)

We will learn more about the [UseBulkOptions](#) method later in our getting started guide.

## Conclusion

In this article, we've explored a high-level overview of how to use our bulk extension methods, how to chain them, and finally, how to use them asynchronously.

One of the major reasons why people appreciate [Dapper](https://www.learndapper.com/) is for its simplicity. We maintained the same approach when we created our Bulk Extension methods in Dapper Plus. We've made them very easy to use and highly customizable through the [hundreds of options](/options) we provide.

If you are more interested by a **100% FREE** solution, you can check our  [Single Extension Methods](single-extensions-methods).