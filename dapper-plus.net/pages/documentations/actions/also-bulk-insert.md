---
Name: Also Bulk Insert
LastMod: 2024-04-24
---

# Also Bulk Insert

## Description

The Dapper Plus AlsoBulkInsert method allows to INSERTs entities in a database table or a view using a lambda expression.

The lambda expression uses the entity or the IEnumerable<TEntity> from the last Bulk[Action] or ThenBulk[Action] chained method. (The action can be an insert, update, delete or merge operation).

## Also Bulk Insert Async

To use chaining with the `AlsoBulkInsertAsync` method, you will need to first call any async method and then use this method.

An `await` is required for each method that you use to perform an operation in your database asynchronously.

In this C# example, we will create a list of order and insert them along with their invoice in our database asynchronously.

```csharp
await (await connection.BulkInsertAsync(orders)).AlsoBulkInsertAsync(order => order.Invoice);
```

## Also Bulk Insert with "One to One" Relation

The Dapper Plus AlsoBulkInsert method allows inserting a related item with a "One to One" relation.


```csharp

//Insert an order and also the related invoice.
connection.BulkInsert(order)
          .AlsoBulkInsert(order => order.Invoice);

//Insert a list of orders and also the related invoice to every order.
connection.BulkInsert(orders)
          .AlsoBulkInsert(order => order.Invoice);
```

## Also Bulk Insert with "One to Many" Relation

The Dapper Plus AlsoBulkInsert method allows inserting related items with a "One to Many" relation.


```csharp
//Insert an order and also all related items.
connection.BulkInsert(order)
          .AlsoBulkInsert(order => order.Items);

//Insert a list of orders and also all related items to every order.
connection.BulkInsert(orders);
          .AlsoBulkInsert(order => order.Items);
```

## Also Bulk Insert and Mixed Relation

The Dapper Plus AlsoBulkInsert method allows inserting related item(s) with any relation.


```csharp

//Insert an order, also all related items and also the related invoice.
connection.BulkInsert(order)
          .AlsoBulkInsert(order => order.Items, order => order.Invoice);

//Insert a list of orders, also all related items to every order and also the related invoice to every order.
connection.BulkInsert(orders)
          .AlsoBulkInsert(order => order.Items, order => order.Invoice);
```

## Also Bulk Insert Chain Action

The Dapper Plus AlsoBulkInsert method allows chaining multiple bulk action methods.


```csharp

//Insert an order and also all related items. Insert an invoice and also all related invoice items.
connection.BulkInsert(order)
          .AlsoBulkInsert(order => order.Items)
          .BulkInsert(invoice)
          .AlsoBulkInsert(invoice => invoice.Items);
//Insert a list of orders and also all related items to every order. Insert a list of invoices and also 
//all related items to every invoice.
connection.BulkInsert(orders)
          .AlsoBulkInsert(order => order.Items)
          .BulkInsert(invoices)
          .AlsoBulkInsert(invoice => invoice.Items);
```
