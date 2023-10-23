---
PermaID: 1000200
Title: Dapper Plus Bulk Merge - Learn how to upsert multiple rows
MetaDescription: Unlock the power of Dapper Plus with the BulkMerge method to insert or update multiple rows from a table. Learn how to use the simplest and fastest way to upsert rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Plus Bulk Merge: Discover how to upsert multiple rows

## Description

Dapper Plus is a 3rd party library that offers some very useful extensions to Dapper. One of these extensions is the [BulkMerge](https://dapper-plus.net/bulk-merge) method that can be used to merge an object into the database, similar to an `UPSERT` operation. 

 - UPSERT refers to operations that update or insert data into a database table. 
 - If the row already exists in the table, then it is updated. 
 - If the row does not exist in the table, then it is inserted. 
 - The UPSERT operation is useful for ensuring that data is synchronized between two systems. 

With `BulkMerge`, you can:

- [Merge single](#example-merge-single)
- [Merge many](#example-merge-many)
- [Merge with relation (One to One)](#example-merge-with-relation-one-to-one)
- [Merge with relation (One to Many)](#example-merge-with-relation-one-to-many)

Bulk Merge helps you merge your entities in a single command. This can come in handy for various reasons, such as reducing database roundtrips or improving performance by avoiding entity lookups.

## Example - Merge Single

You can use the `BulkMerge` method to merge (insert or update) a single record. The following example merges a customer record into the **Customers** table.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkMerge(new List<Customer>() { new Customer() { CustomerName = "ExampleBulkMerge", ContactName = "Example Name :" +  1}});
}        
```

In the above example, If the customer already exists in the table, then it will be updated, but if the customer does not exist in the table, then it will be inserted as a new record.

Try it: [.NET Core](https://dotnetfiddle.net/MVW9uO) | [.NET Framework](https://dotnetfiddle.net/EOby5U)

## Example - Merge Many

The `BulkMerge` method can merge multiple records with a single database call can significantly improve performance. It merges multiple entities with Bulk Operation. The following example merges a list of customers into the **Customers** table.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkMerge(customers);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/qHxfT9) | [.NET Framework](https://dotnetfiddle.net/Ncp8RC)

## Example - Merge with relation (One to One)

`BulkMerge` allows you to merge related entities to the database in one database operation. It is especially useful when merging a large number of entities that have relationships with each other. 

To use `BulkMerge` when entities have a relationship, simply specify the relationship between the entities when you configure the bulk operation.

The following example shows how to use the `BulkMerge` with a one-to-one relationship between the entities.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    connection.BulkMerge(suppliers).ThenForEach(x => x.Product.SupplierID = x.SupplierID).ThenBulkMerge(x => x.Product);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/1lGd5X) | [.NET Framework](https://dotnetfiddle.net/zUIMnK)

## Example - Merge with relation (One to Many)

Dapper Plus also allows you to merge a list of entities with a one-to-many relationship with Bulk Operation as shown in the following example.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkMerge(suppliers).ThenForEach(x => x.Products.ForEach(y => y.SupplierID =  x.SupplierID)).ThenBulkMerge(x => x.Products);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/bYDz5C) | [.NET Framework](https://dotnetfiddle.net/YM0zev)

## Related Articles

- [Dapper Plus Website](https://dapper-plus.net/)
- [Bulk Merge](https://dapper-plus.net/bulk-merge)
