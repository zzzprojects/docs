---
PermaID: 1000198
Title: Dapper Plus Bulk Delete - Learn how to delete multiple rows
MetaDescription: Unlock the power of Dapper Plus with the BulkDelete method to delete multiple rows from a table. Learn how to use the simplest and fastest way to delete rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Plus Bulk Delete: Discover how to delete multiple rows

## Description

The [BulkDelete](https://dapper-plus.net/bulk-delete) method allows you to delete multiple entities in one database call. It is useful when you want to delete a large number of records from a table. You can use the `BulkDelete` method to delete all records from a table, or you can specify a filter condition to only delete certain records. 

It deletes entities using Bulk Operation. With `BulkDelete`, you can:

- [Delete single](#example-delete-single)
- [Delete many](#example-delete-many)
- [Delete with relation (One to One)](#example-delete-with-relation-one-to-one)
- [Delete with relation (One to Many)](#example-delete-with-relation-one-to-many)

To use the `BulkDelete` method, you simply need to specify the list of entities that you want to delete.

## Example - Delete Single

You can use the `BulkDelete` method to delete a single record. The following example deletes a single record from the **Customers** table.

```csharp    
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkDelete(connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53,57) ").ToList());
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/cAcidj) | [.NET Framework](https://dotnetfiddle.net/Eu7Xoj)

## Example - Delete Many

The `BulkDelete` method can delete multiple records with a single database call which can significantly improve performance by deleting multiple entities with Bulk Operation. The following example deletes a list of customers from the **Customers** table.

```csharp
DapperPlusManager.Entity<Customer>().Table("Customers").Key("CustomerID");

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkDelete(connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerID in (53,57) ").ToList());
}    
```
Try it: [.NET Core](https://dotnetfiddle.net/81AUjA) | [.NET Framework](https://dotnetfiddle.net/qmClqw)

## Example - Delete with relation (One to One)

`BulkDelete` allows you to delete related entities from the database in one database operation. It is especially useful when deleting a large number of entities that have relationships with each other. 

To use `BulkDelete` when entities have a relationship, simply specify the relationship between the entities when you configure the bulk operation.

The following example shows how to use the `BulkDelete` with a one-to-one relationship between the entities.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkDelete(suppliers.Select(x => x.Product)).BulkDelete(suppliers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/jLDQj3) | [.NET Framework](https://dotnetfiddle.net/U6CGtD)

## Example - Delete with relation (One to Many)

Dapper Plus also allows you to delete a list of entities with a one-to-many relationship with Bulk Operation as shown in the following example.

```csharp
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkDelete(suppliers.SelectMany(x => x.Products)).BulkDelete(suppliers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/BaIldo) | [.NET Framework](https://dotnetfiddle.net/7BVhC5)

## Related Articles

- [Dapper Plus Website](https://dapper-plus.net/)
- [Bulk Delete](https://dapper-plus.net/bulk-delete)
