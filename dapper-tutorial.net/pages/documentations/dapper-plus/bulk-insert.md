---
PermaID: 1000199
Name: Bulk Insert
---

# Dapper Plus - Bulk Insert

## Description

INSERT entities using the Bulk Operation.

- [Insert single](#example---insert-single)
- [Insert many](#example---insert-many)
- [Insert with relation (One to One)](#example---insert-with-relation-one-to-one)
- [Insert with relation (One to Many)](#example---insert-with-relation-one-to-many)

## Example - Insert Single

INSERT a single entity with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkInsert(new List<Customer>() { new Customer() { CustomerName = "ExampleBulkInsert", ContactName = "Example Name :" +  1}});
}        
```
Try it: [.NET Core](https://dotnetfiddle.net/jPJxKl) | [.NET Framework](https://dotnetfiddle.net/swvvDb)

## Example - Insert Many
INSERT many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkInsert(customers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/jPY8xy) | [.NET Framework](https://dotnetfiddle.net/3Z4SzH)

## Example - Insert with relation (One to One)
INSERT entities with a one to one relation with Bulk Operation.

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
INSERT entities with a one to many relations with Bulk Operation.

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

By default, the `BulkInsert` method already returns the identity when inserting.

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
