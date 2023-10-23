---
PermaID: 1000199
Title: Dapper Plus Bulk Insert - Learn how to insert multiple rows
MetaDescription: Unlock the power of Dapper Plus with the BulkInsert method to insert multiple rows from a table. Learn how to use the simplest and fastest way to insert rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Plus Bulk Insert: Discover how to insert multiple rows

## Description

Dapper Plus provides an extension method called [BulkInsert](https://dapper-plus.net/bulk-insert) which is a high-performance bulk insert command for Dapper. It can insert multiple rows into a database table at once. It inserts entities using the **Bulk Operation**.

It is faster than standard insert because it reduces the number of database round trips. `BulkInsert` can also be used to load data from a file or stream.

With `BulkInsert`, you can:

- [Insert single](#example-insert-single)
- [Insert many](#example-insert-many)
- [Insert with relation (One to One)](#example-insert-with-relation-one-to-one)
- [Insert with relation (One to Many)](#example-insert-with-relation-one-to-many)

This can be useful when inserting a large number of records into a database table. For example, if you need to insert 1 million rows, `BulkInsert` can do it in just a few seconds.

## Example - Insert Single

You can use the `BulkInsert` method to insert a single record. The following example inserts a new record into the **Customers** table.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkInsert(new List<Customer>() { new Customer() { CustomerName = "ExampleBulkInsert", ContactName = "Example Name :" +  1}});
}        
```

Try it: [.NET Core](https://dotnetfiddle.net/jPJxKl) | [.NET Framework](https://dotnetfiddle.net/swvvDb)

## Example - Insert Many

The ability to insert multiple records with a single database call can significantly improve performance. It inserts many entities with Bulk Operation. The following example inserts a list of customers into the **Customers** table.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkInsert(customers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/jPY8xy) | [.NET Framework](https://dotnetfiddle.net/3Z4SzH)

## Example - Insert with relation (One to One)

`BulkInsert` allows you to insert related entities into the database in one operation. It is especially useful when inserting a large number of entities that have relationships with each other. 

To use `BulkInsert` when entities have a relationship, simply specify the relationship between the entities when you configure the bulk operation.

The following example shows how to use the `BulkInsert` with a one-to-one relationship between the entities.

```csharp    
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    connection.BulkInsert(suppliers).ThenForEach(x => x.Product.SupplierID = x.SupplierID).ThenBulkInsert(x => x.Product);
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/9EwA7g) | [.NET Framework](https://dotnetfiddle.net/tEZywR)

## Example - Insert with relation (One to Many)

Dapper Plus can insert a list of entities with a one-to-many relationship with Bulk Operation as shown in the following example.

```csharp    
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID); 
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);     

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    connection.BulkInsert(suppliers).ThenForEach(x => x.Products.ForEach(y => y.SupplierID =  x.SupplierID)).ThenBulkInsert(x => x.Products);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/4EG5mH) | [.NET Framework](https://dotnetfiddle.net/NbCYoZ)

## Real-Life Scenarios

### Insert and keep identity value

Your entity has an identity property, but you want to force it to insert a specific value instead. The `InsertKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
        
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.UseBulkOptions(options => options.InsertKeepIdentity = true).BulkInsert(customers);
}    
```

Try it: [.NET Core](https://dotnetfiddle.net/SxF6Eb) | [.NET Framework](https://dotnetfiddle.net/9YcUe8)

### Insert without returning the identity value

By default, the `BulkInsert` method already returns the identity when inserted.

However, such behavior impacts performance. For example, when the identity must be returned, a temporary table is created in SQL Server instead of directly using `SqlBulkCopy` into the destination table.

You can improve your performance by turning off the AutoMapOutput option.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
        
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.UseBulkOptions(options => options.AutoMapOutputDirection = false).BulkInsert(customers);
            
    FiddleHelper.WriteTable("1 - Customers (from list)", customers);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/lDwDOS) | [.NET Framework](https://dotnetfiddle.net/Zbf1Qk)

## Related Articles

- [Dapper Plus Website](https://dapper-plus.net/)
- [Dapper Plus Bulk Insert](https://dapper-plus.net/bulk-insert)
