---
PermaID: 1000202
Name: Plus
---

# Dapper Plus

## What's Dapper Plus?
Dapper Plus extends the IDbConnection interface with Bulk Operations methods:

- Bulk Insert
- Bulk Update
- Bulk Delete
- Bulk Merge

This library is the **fastest way** to perform saving operations in a database.

Official Website: <a href="http://dapper-plus.net/" target="_blank">http://dapper-plus.net/</a>

## Installation
Dapper Plus is installed through NuGet: <a href="https://www.nuget.org/packages/Z.Dapper.Plus/" target="_blank">https://www.nuget.org/packages/Z.Dapper.Plus/</a>

This library is **NOT FREE**, but a monthly trial is available at the start of every month.

## Requirement
Dapper Plus is compatible with all major database provider:

- SQL Server 2008+
- SQL Azure
- SQL Compact
- Oracle
- MySQL
- SQLite
- PostgreSQL

## Methods
Dapper Plus extend your IDbConnection interface with multiple methods:

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
