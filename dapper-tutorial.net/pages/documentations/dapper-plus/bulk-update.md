---
PermaID: 1000201
Name: Bulk Update
---

# Dapper Plus - Bulk Update

## Description

The `BulkUpdate` extension method let you update a large number of entities in your database using Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(customers);
}
```

[Try it](https://dotnetfiddle.net/lX7yj6)

- [Bulk Update with options](#example---bulk-update-with-options)
- [Update single](#example---update-single)
- [Update many](#example---update-many)
- [Update with relation (One to One)](#example---update-with-relation-one-to-one)
- [Update with relation (One to Many)](#example---update-with-relation-one-to-many)

## Example - Bulk Insert with options

You can customize `BulkUpdate` operation with different options which is available using the `UseBulkOptions`. The options parameter let you use a lambda expression to customize the way entities are updated.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.UseBulkOptions(options => options.BatchSize = 100).BulkUpdate(customers);
}		
```
[Try it](https://dotnetfiddle.net/PN85Ym)

## Example - Update Single
UPDATE a single entity with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(customers);
}	
```
[Try it](https://dotnetfiddle.net/o1WkMA)

## Example - Update Many
UPDATE many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(customers);
}	
```
[Try it](hhttps://dotnetfiddle.net/10RLzV)

## Example - Update with relation (One to One)
UPDATE entities with a one to one relation with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{	
    connection.BulkUpdate(suppliers, x => x.Product);
}		
```
[Try it](https://dotnetfiddle.net/rwjvqz)

## Example - Update with relation (One to Many)
UPDATE entities with a one to many relation with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(suppliers, x => x.Products);
}
```
[Try it](https://dotnetfiddle.net/fsTfEg)
