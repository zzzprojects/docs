---
PermaID: 1000202
Name: Getting Started with Dapper Plus
---

# Dapper Plus

## What's Dapper Plus?

Dapper Plus is a high-performance micro-ORM library. It provides an easy way to map your classes to database tables, and vice versa. 

Dapper Plus also offers support for bulk operations, which can significantly improve the performance of your application. Dapper Plus extends the IDbConnection interface with Bulk Operations methods:

- Bulk Insert
- Bulk Update
- Bulk Delete
- Bulk Merge

This library is the **fastest way** to perform saving operations in a database.

Official Website: <a href="https://dapper-plus.net/" target="_blank">https://dapper-plus.net/</a>

## Installation

Using Dapper Plus is extremely easy and you can install Dapper Plus through NuGet: <a href="https://www.nuget.org/packages/Z.Dapper.Plus/" target="_blank">https://www.nuget.org/packages/Z.Dapper.Plus/</a> 

To install the Dapper NuGet Package, run the following command in the **Package Manager Console**:

```csharp
PM> Install-Package Z.Dapper.Plus
```

Once you have the package installed, you can begin mapping your classes to database tables. 

This library is **NOT FREE**, but a monthly trial is available at the start of every month.

## Requirement

Dapper Plus is compatible with all major database providers and there is no database-specific implementation.

- SQL Server 2008+
- SQL Azure
- SQL Compact
- Oracle
- MySQL
- SQLite
- PostgreSQL

Dapper Plus requires .NET 4.0 or higher. Additionally, you will need to install the following NuGet package.

- Dapper (latest version)

Once you have installed the required NuGet packages, you can start using Dapper in your project.

## Methods

Dapper Plus extends your `IDbConnection` interface with multiple methods that can be used to extend the functionality of the library, especially the bulk operations (insert, update, delete, merge) on your database in just one line of code. 

- [Bulk Insert](/bulk-insert)
- [Bulk Update](/bulk-update)
- [Bulk Merge](/bulk-merge)
- [Bulk Delete](/bulk-delete)

```csharp
// STEP MAPPING
DapperPlusManager.Entity<Supplier>().Table("Suppliers").Identity(x => x.SupplierID);
DapperPlusManager.Entity<Product>().Table("Products").Identity(x => x.ProductID);

// STEP BULKINSERT
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkInsert(suppliers).ThenForEach(x => x.Products.ForEach(y => y.SupplierID =  x.SupplierID)).ThenBulkInsert(x => x.Products);
}

// STEP BULKUPDATE
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkUpdate(suppliers, x => x.Products);
}

// STEP BULKMERGE
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkMerge(suppliers).ThenForEach(x => x.Products.ForEach(y => y.SupplierID =  x.SupplierID)).ThenBulkMerge(x => x.Products);
}

// STEP BULKDELETE
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    connection.BulkDelete(suppliers.SelectMany(x => x.Products)).BulkDelete(suppliers);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/CPzM2X) | [.NET Framework](https://dotnetfiddle.net/dbMVfr)
