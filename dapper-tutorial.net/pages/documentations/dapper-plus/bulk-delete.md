---
PermaID: 1000198
Name: Bulk Delete
---

# Dapper Plus - Bulk Delete

## Description

The Dapper Plus `BulkDelete` method allows to DELETE entities in a database table or a view using Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var customers = connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53, 57) ").ToList();
    connection.BulkDelete(customers);
}	
```
[Try it](https://dotnetfiddle.net/YAFDeo)

- [Bulk Delete with options](#example---bulk-delete-with-options)
- [Delete single](#example---delete-single)
- [Delete many](#example---delete-many)
- [Delete with relation (One to One)](#example---delete-with-relation-one-to-one)
- [Delete with relation (One to Many)](#example---delete-with-relation-one-to-many)

## Example - Bulk Delete with options

You can customize `BulkDelete` operation with different options which is available using the `UseBulkOptions`. The options parameter let you use a lambda expression to customize the way entities are deleted.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.UseBulkOptions(options => options.BatchSize = 100).BulkUpdate(customers);
}		
```
[Try it](https://dotnetfiddle.net/kOnxPy)

## Example - Delete Single
DELETE a single entity with Bulk Operation.

```csharp	
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkDelete(connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53,57) ").ToList());
}	
```
[Try it](https://dotnetfiddle.net/Eu7Xoj)

## Example - Delete Many
DELETE many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkDelete(connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53,57) ").ToList());
}	
```
[Try it](https://dotnetfiddle.net/qmClqw)

## Example - Delete with relation (One to One)
DELETE entities with a one to one relation with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkDelete(suppliers.Select(x => x.Product)).BulkDelete(suppliers);
}
```
[Try it](https://dotnetfiddle.net/U6CGtD)

## Example - Delete with relation (One to Many)
DELETE entities with a one to many relation with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkDelete(suppliers.SelectMany(x => x.Products)).BulkDelete(suppliers);
}
```
[Try it](https://dotnetfiddle.net/7BVhC5)
