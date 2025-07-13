---
title: JSON Columns in Entity Framework Core
description: EF Core 7.0 contains provider-agnostic support for JSON columns, with an implementation for SQL Server. This support allows the mapping of aggregates built from .NET types to JSON documents. 
canonical: /misc/sparse-columns
status: Published
lastmod: 2025-07-13
---

# EF Core JSON Columns

JSON columns are a type of database column that can store JSON (JavaScript Object Notation) data. JSON is a lightweight data-interchange format that is easy for humans to read and write and for machines to parse and generate.

 - In the context of databases, JSON columns allow for storing structured or semi-structured data flexibly and efficiently. 
 - This can be particularly useful when dealing with data that has varying or unpredictable structure, such as user-generated content or log data. 
 - Instead of creating a separate column for each data element, a JSON column can store all the data in a single column.
 - JSON columns are supported by several popular relational databases, including SQL Server, PostgreSQL, MySQL, and MariaDB. 

## Mapping to JSON Columns

EF Core 7.0 contains provider-agnostic support for JSON columns, with an implementation for SQL Server. This support allows the mapping of aggregates built from .NET types to JSON documents. 

 - In Entity Framework (EF) Core, JSON columns can be used to store and query JSON data in a database. 
 - EF Core supports mapping JSON columns to .NET types, which allows you to work with JSON data more naturally and conveniently.
 - EF Core 7.0 also supports updating and saving changes to JSON documents.

Aggregate types can be defined in EF Core using the `OwnsOne` and `OwnsMany` methods. For example, let's take the aggregate type used in our sample model for storing contact information.

```csharp
public class Customer
{
    public int Id { get; set; }
    public string? Name { get; set; }
    public ContactDetails? Contact { get; set; }
}

public class ContactDetails
{
    public Address Address { get; set; } = null!;
    public string? Phone { get; set; }
}

public class Address
{
    public string? Street { get; set; }
    public string City { get; set; }
    public string? Postcode { get; set; }
    public string Country { get; set; }
}
```

The aggregate type is configured in `OnModelCreating` using `OwnsOne`:

```csharp
public class CustomerContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=EmployeesDb;");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Customer>().OwnsOne(
            customer => customer.Contact, ownedNavigationBuilder =>
            {
                ownedNavigationBuilder.ToJson();
                ownedNavigationBuilder.OwnsOne(contactDetails => contactDetails.Address);
            });
    }
}
```

When calling the `ToJson` method in configuring the aggregate type, the `Customers` table will now contain a JSON column for `ContactDetails` populated with a JSON document for each customer:

```csharp
CREATE TABLE [dbo].[Customers] (
    [Id]      INT            IDENTITY (1, 1) NOT NULL,
    [Name]    NVARCHAR (MAX) NULL,
    [Contact] NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_Customers] PRIMARY KEY CLUSTERED ([Id] ASC)
);
```

## Queries JSON columns

To retrieve information from JSON columns in EF Core, the process is identical to querying other aggregate types. The following examples show how to query JSON columns.

A query for all customers that live in Paris:

```csharp
using (var context = new CustomerContext())
{
    var customersInParis = context.Customers
        .Where(customer => customer.Contact.Address.City == "Paris")
        .ToList();
}
```

## Pros & Cons

One advantage of using JSON columns is that they can support complex querying and indexing. For example, you can use SQL functions to extract specific values from the JSON data or create indexes to speed up queries that involve specific fields within the JSON.

However, using JSON columns can also have some downsides. For example, they can make it more difficult to enforce data consistency and integrity, as the structure of the data is not strictly defined. Additionally, querying JSON data can be more complex and slower than querying structured data stored in traditional columns.
