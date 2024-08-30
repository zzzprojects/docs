---
PermaID: 1000209
Title: Dapper.Plus - A Getting Started Tutorial for Bulk CRUD Operations
MetaDescription: Discover Dapper.Plus Library to perform CRUD operations such as BulkInsert, BulkUpdate, BulkDelete, BulkMerge. Learn about Dapper.Plus limitations and how to install it with NuGet.
LastMod: 2024-08-30
---

# Dapper.Plus: A Getting Started Tutorial for Bulk CRUD Operations

## Overview

**Dapper.Plus** is a small library that you can add to your project which will extend your `IDbConnection` and `IDbTransaction` interfaces with highly efficient Bulk operations methods.

- <a href="https://dapper-plus.net/bulk-insert" target="_blank">Bulk Insert</a>
- <a href="https://dapper-plus.net/bulk-update" target="_blank">Bulk Update</a>
- <a href="https://dapper-plus.net/bulk-delete" target="_blank">Bulk Delete</a>
- <a href="https://dapper-plus.net/bulk-merge" target="_blank">Bulk Merge</a>

Using this library, you can perform saving operations in the **fastest way**. It can be used with or without Dapper, and it is also compatible with all other Dapper packages.

## NuGet Installation

**Dapper.Plus** is only available through NuGet: <a href="https://www.nuget.org/packages/Z.Dapper.Plus/" target="_blank">https://www.nuget.org/packages/Z.Dapper.Plus/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Z.Dapper.Plus
```

## PRO Version

Every month, a **FREE** trial of the **PRO** version is available to let you evaluate all its features without limitations.

| Features | PRO |
| :---------- | :----- |
| Bulk Insert | Yes |
| Bulk Delete | Yes |
| Bulk Update | Yes |
| Bulk Merge | Yes |
| Bulk Action Async | Yes |
| Bulk Also Action | Yes |
| Bulk Then Action | Yes |
| Commercial License | Yes |
| Royalty-Free | Yes |
| Support & Upgrades (1 year) | Yes |

More information can be found at: <a href="https://dapper-plus.net/" target="_blank">https://dapper-plus.net/</a>

## APIs

Once you installed this library then the following extension methods will automatically add to `DbConnection`:

- <a href="https://dapper-plus.net/bulk-insert" target="_blank">Bulk Insert</a>
- <a href="https://dapper-plus.net/bulk-update" target="_blank">Bulk Update</a>
- <a href="https://dapper-plus.net/bulk-delete" target="_blank">Bulk Delete</a>
- <a href="https://dapper-plus.net/bulk-merge" target="_blank">Bulk Merge</a>

There is no configuration required and no need for extra coding. You can easily use these extension methods in your code.

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
{% include component-try-it.html href='https://dotnetfiddle.net/NcILLg' %}

You can find the detailed documentation here: <a href="https://dapper-plus.net/overview" target="_blank">https://dapper-plus.net/overview</a>

## DB Provider Supported

**Dapper.Plus** is compatible with all major database providers:

- SQL Server 2008+
- SQL Azure
- SQL Compact
- Oracle
- MySQL
- MariaDB
- SQLite
- PostgreSQL

## Support

If you have any further queries, contact the outstanding customer support for any request, and you will get your answer within the next business day, hour, or minute!
- <a href="mailto:info@zzzprojects.com">info@zzzprojects.com</a>
