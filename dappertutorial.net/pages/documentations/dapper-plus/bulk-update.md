---
PermaID: 1000201
Title: Dapper Plus Bulk Update - Learn how to update multiple rows
MetaDescription: Unlock the power of Dapper Plus with the BulkUpdate method to update multiple rows from a table. Learn how to use the simplest and fastest way to update rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Plus Bulk Update: Discover how to update multiple rows

## Description

Dapper Plus provides an extension method called [BulkUpdate](https://dapper-plus.net/bulk-update) that is used to update multiple records in a database table at once. It is the fastest and most efficient way to update entities in your database. It can update multiple entities in one database round trip

 - The `BulkUpdate` method takes a list of entities as a parameter and updates each entity in the database.
 - To use the `BulkUpdate` method, you first need to create a list of entities that you want to update. 
 - Then, you can call the `BulkUpdate` method and pass in the list of entities. 
 - The method will update each entity in the database.

It updates entities using **Bulk Operation** and with `BulkUpdate`, you can:

- [Update single](#example-update-single)
- [Update many](#example-update-many)
- [Update with relation (One to One)](#example-update-with-relation-one-to-one)
- [Update with relation (One to Many)](#example-update-with-relation-one-to-many)

## Example - Update Single

You can use the `BulkUpdate` method to update a single record. The following example updates a single record into the **Customers** table.

```csharp
List<Customer> customers;

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	customers = connection.Query<Customer>("Select Top 1 * FROM CUSTOMERS").ToList();
}

customers.ForEach(x => x.CustomerName = "ExampleBulkUpdate");

DapperPlusManager.Entity<Customer>().Table("Customers"); 

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	connection.BulkUpdate(customers);
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/wUbog7) | [.NET Framework](https://dotnetfiddle.net/o1WkMA)

## Example - Update Many

The ability to update multiple records with a single database call can significantly improve performance. It updates many entities with Bulk Operations. The following example updates a list of customers into the **Customers** table.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(customers);
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/23NZSH) | [.NET Framework](hhttps://dotnetfiddle.net/10RLzV)

## Example - Update with relation (One to One)

`BulkUpdate` allows you to update related entities in the database in one operation. It is especially useful when updating a large number of entities that have relationships with each other. 

To use `BulkUpdate` when entities have a relationship, simply specify the relationship between the entities when you configure the bulk operation.

The following example shows how to use the `BulkUpdate` with a one-to-one relationship between the entities.

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

Dapper Plus can also allow you to update a list of entities with a one-to-many relationship with Bulk Operation as shown in the following example.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(suppliers, x => x.Products);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/l799dF) | [.NET Framework](https://dotnetfiddle.net/fsTfEg)

## Related Articles

- [Dapper Plus Website](https://dapper-plus.net/)
- [Bulk Update](https://dapper-plus.net/bulk-update)