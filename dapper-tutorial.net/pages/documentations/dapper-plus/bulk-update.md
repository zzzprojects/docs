---
PermaID: 1000201
Name: Bulk Update
---

# Dapper Plus - Bulk Update

## Description

UPDATE entities using Bulk Operation.

- [Update single](#example---update-single)
- [Update many](#example---update-many)
- [Update with relation (One to One)](#example---update-with-relation-one-to-one)
- [Update with relation (One to Many)](#example---update-with-relation-one-to-many)

## Example - Update Single
UPDATE a single entity with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(customers);
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/wUbog7) | [.NET Framework](https://dotnetfiddle.net/o1WkMA)

## Example - Update Many
UPDATE many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(customers);
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/23NZSH) | [.NET Framework](hhttps://dotnetfiddle.net/10RLzV)

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
Try it: [.NET Core](https://dotnetfiddle.net/xqbyE7) | [.NET Framework](https://dotnetfiddle.net/rwjvqz)

## Example - Update with relation (One to Many)
UPDATE entities with a one to many relations with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(suppliers, x => x.Products);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/l799dF) | [.NET Framework](https://dotnetfiddle.net/fsTfEg)
