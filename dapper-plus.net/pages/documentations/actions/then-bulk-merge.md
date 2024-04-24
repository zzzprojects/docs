---
Name: Then Bulk Merge
LastMod: 2024-04-24
---

# Then Bulk Merge

## Description

The Dapper Plus ThenBulkMerge method allows to MERGE entities in a database table or a view using a lambda expression.

The lambda expression uses the entity or the IEnumerable<TEntity> from the last Bulk[Action] or ThenBulk[Action] chained method. (The action can be an insert, update, delete or merge operation).

## Then Bulk Merge Async

To perform chaining with the `ThenBulkMergeAsync` method, begin with an asynchronous operation and then add this method.

Each asynchronous database operation requires an `await` to ensure it completes properly.

In this C# example, we show how to merge a list of orders and their associated invoices into our database asynchronously:

```csharp
await (await connection.BulkMergeAsync(orders)).ThenBulkMergeAsync(order => order.Invoice);
```

## Then Bulk Merge with "One to One" Relation

The Dapper Plus ThenBulkMerge method allows merging a related item with a "One to One" relation.


```csharp

//Merge an order and then the related invoice.
connection.BulkMerge(orderTransaction)
          .ThenBulkMerge(transaction => transaction.order)
          .ThenBulkMerge(order => order.Invoice);

//Merge a list of orders and then the related invoice to every order.
connection.BulkMerge(orderTransaction)
          .ThenBulkMerge(transaction => transaction.orders)
          .ThenBulkMerge(order => order.Invoice);
```

## Then Bulk Merge with "One to Many" Relation

The Dapper Plus ThenBulkMerge method allows merging related items with a "One to Many" relation.


```csharp

//Merge an order and then all related items.
connection.BulkMerge(orderTransaction)
          .ThenBulkMerge(transaction => transaction.order)
          .ThenBulkMerge(order => order.Items);

//Merge a list of orders and then all related items to every order.
connection.BulkMerge(orderTransaction)
          .ThenBulkMerge(transaction => transaction.orders);
          .ThenBulkMerge(order => order.Items);
```

## Then Bulk Merge Chaining

The Dapper Plus ThenBulkMerge method allows chaining multiple bulk action methods.


```csharp

//Merge an order and then all related items. Merge an invoice and then all related items.
connection.BulkMerge(orderTransaction)
          .ThenBulkMerge(transaction => transaction.order)
          .ThenBulkMerge(order => order.Items)
          .BulkMerge(invoiceTransaction)
          .ThenBulkMerge(transaction => transaction.invoice)
          .ThenBulkMerge(invoice => invoice.Items);

//Merge a list of orders and then all related items to every order. Merge a list of invoices and then all related items to every invoice.
connection.BulkMerge(orderTransaction)
          .ThenBulkMerge(transaction => transaction.orders)
          .ThenBulkMerge(order => order.Items)
          .BulkMerge(invoiceTransaction)
          .ThenBulkMerge(transaction => transaction.invoices)
          .ThenBulkMerge(invoice => invoice.Items);
```
