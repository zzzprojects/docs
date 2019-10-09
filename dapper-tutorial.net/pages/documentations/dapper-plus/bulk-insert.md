---
PermaID: 1000199
Name: Bulk Insert
---

# Dapper Plus - Bulk Insert

## Description

The `BulkInsert` extension method let you insert a large number of entities in your database using Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkInsert(customers);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/FLGNyE' %}

- [Bulk Insert with options](#example---bulk-insert-with-options)
- [Insert single](#example---insert-single)
- [Insert many](#example---insert-many)
- [Insert with relation (One to One)](#example---insert-with-relation-one-to-one)
- [Insert with relation (One to Many)](#example---insert-with-relation-one-to-many)

## Example - Bulk Insert with options

You can customize `BulkInsert` operation with different options which is available using the `UseBulkOptions`. The options parameter let you use a lambda expression to customize the way entities are inserted.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.UseBulkOptions(options => options.BatchSize = 100).BulkInsert(customers);
}		
```
{% include component-try-it.html href='https://dotnetfiddle.net/uMfPES' %}

## Example - Insert Single

INSERT a single entity with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkInsert(new List<Customer>() { new Customer() { CustomerName = "ExampleBulkInsert", ContactName = "Example Name :" +  1}});
}		
```
{% include component-try-it.html href='https://dotnetfiddle.net/swvvDb' %}

## Example - Insert Many
INSERT many entities with Bulk Operation.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkInsert(customers);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/3Z4SzH' %}

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
{% include component-try-it.html href='https://dotnetfiddle.net/tEZywR' %}

## Example - Insert with relation (One to Many)
INSERT entities with a one to many relation with Bulk Operation.

```csharp	
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID); 
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID); 	

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{	
	connection.BulkInsert(suppliers).ThenForEach(x => x.Products.ForEach(y => y.SupplierID =  x.SupplierID)).ThenBulkInsert(x => x.Products);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/NbCYoZ' %}

## Real Life Scenarios

### Insert and keep identity value

Your entity has an identity property, but you want to force to insert a specific value instead. The `InsertKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers"); 
		
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.UseBulkOptions(options => options.InsertKeepIdentity = true).BulkInsert(customers);
}	
```

{% include component-try-it.html href='https://dotnetfiddle.net/9YcUe8' %}

### Insert without returning identity value

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

{% include component-try-it.html href='https://dotnetfiddle.net/Zbf1Qk' %}
