---
PermaID: 1000198
Name: Bulk Delete
---

# Dapper Plus - Bulk Delete

## Description
DELETE entities using Bulk Operation.

- [Delete single](#example---delete-single)
- [Delete many](#example---delete-many)
- [Delete with relation (One to One)](#example---delete-with-relation-one-to-one)
- [Delete with relation (One to Many)](#example---delete-with-relation-one-to-many)

## Example - Delete Single
DELETE a single entity with Bulk Operation.

```csharp	
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkDelete(connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53,57) ").ToList());
}	
```
{% include component-try-it.html href='https://dotnetfiddle.net/v9D2sE' %}

## Example - Delete Many
DELETE many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkDelete(connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53,57) ").ToList());
}	
```
{% include component-try-it.html href='https://dotnetfiddle.net/aYkEwF' %}

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
{% include component-try-it.html href='https://dotnetfiddle.net/9qGgQv' %}

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
{% include component-try-it.html href='https://dotnetfiddle.net/SEGvy9' %}
