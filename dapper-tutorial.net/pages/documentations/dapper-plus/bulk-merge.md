---
PermaID: 1000200
Name: Bulk Merge
---

# Dapper Plus - Bulk Merge

## Description
MERGE entities using Bulk Operation.

- [Merge single](#example---merge-single)
- [Merge many](#example---merge-many)
- [Merge with relation (One to One)](#example---merge-with-relation-one-to-one)
- [Merge with relation (One to Many)](#example---merge-with-relation-one-to-many)

## Example - Merge Single
MERGE a single entity with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkMerge(new List<Customer>() { new Customer() { CustomerName = "ExampleBulkMerge", ContactName = "Example Name :" +  1}});
}		
```
{% include component-try-it.html href='https://dotnetfiddle.net/U6g6Gd' %}

## Example - Merge Many
MERGE many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkMerge(customers);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/T3R43T' %}

## Example - Merge with relation (One to One)
MERGE entities with a one to one relation with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{	
	connection.BulkMerge(suppliers).ThenForEach(x => x.Product.SupplierID = x.SupplierID).ThenBulkMerge(x => x.Product);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/CJfb4l' %}

## Example - Merge with relation (One to Many)
MERGE entities with a one to many relation with Bulk Operation.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkMerge(suppliers).ThenForEach(x => x.Products.ForEach(y => y.SupplierID =  x.SupplierID)).ThenBulkMerge(x => x.Products);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/9C5Yd2' %}
