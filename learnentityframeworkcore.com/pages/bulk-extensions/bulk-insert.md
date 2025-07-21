---
title: EF Core Bulk Insert Extensions
description: Entity Framework Extensions BulkInsert method provides the ability to efficiently insert a list of objects. It uses the same pattern as EF Core's AddRange, but much faster.
canonical: /bulk-extensions/bulk-insert
status: Published
lastmod: 2025-07-10
---

# EF Core Bulk Insert

EF Core provides the `Add` and `AddRange` methods for inserting data. However, a database roundtrip is required for every data you insert. While the performance is better in EF Core than the old EF6, the solution becomes very inefficient when you need to insert thousands of entities.

The **fastest way of inserting** multiple data is by using the [Entity Framework Extensions](https://entityframework-extensions.net/) third-party library.

Depending on the provider, performance can be increased by up to **50x faster** and more.

## Bulk Extensions Insert

The `BulkInsert` extension method is a feature provided by the [Entity Framework Extensions](https://entityframework-extensions.net/) library for inserting a large number of entities into a database in a single operation. This method can improve the performance of inserting large amounts of data into a database by reducing the number of database round-trips required.

The `BulkInsert` method can be used in conjunction with the `DbContext` or the `DbSet`, and it accepts a collection of entities as a parameter. The method internally uses the underlying database provider's bulk insert feature, such as `SqlBulkCopy` for SQL Server, to perform the operation.

It also supports options like:

 - Setting the batch size to control the number of entities that are inserted at a time.
 - Mapping properties to columns.
 - Setting the timeout and more.

The `BulkInsert` method is useful when you need to insert a large number of entities into a database, such as when performing data migration or when working with large datasets. It can significantly improve the performance of inserting data into a database.

Here's an example of how to use the `BulkInsert` method of the [Entity Framework Extensions](https://entityframework-extensions.net/) library:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore

using (var context = new MyDbContext())
{
    var customers = new List<Customer>
    {
        new Customer { Name = "John Smith", Age = 30 },
        new Customer { Name = "Jane Doe", Age = 25 },
        new Customer { Name = "Bob Johnson", Age = 40 },
    };

    // Insert all customers with BulkInsert
    context.BulkInsert(customers);
}
```

In this example, we're creating a new instance of a `DbContext` (in this case, `MyDbContext`) and then creating a list of `Customer` objects. We then use the `BulkInsert` method to insert all of the customers in the list into the database in a single, efficient operation.

## Note

 - NuGet Package: [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
 - Documentation: [Entity Framework Extensions – Bulk Insert](https://entityframework-extensions.net/bulk-insert)
