---
PermaID: 1000212
Title: Dapper.SimpleSave - A Getting Started Tutorial for CRUD Operations
MetaDescription: Discover Dapper.SimpleSave Library to perform CRUD operations such as Create, CreateAll, Update, UpdateAll, Delete, DeleteAll, SoftDelete, SoftDeleteAll. Learn about Dapper.SimpleSave limitations and how to install it with NuGet.
LastMod: 2023-10-23
---

# Dapper.SimpleSave: A Getting Started Tutorial for CRUD Operations

## Overview

**Dapper.SimpleSave** is a small library package that adds basic CRUD operations to Dapper. Dapper easily retrieves data from the database when a single object is involved, but when you are dealing with complex hierarchies, then dapper is not very helpful. 

This is where **Dapper.SimpleSave** comes in and saves complex objects to the database in a very simple way.

 - **Dapper.SimpleSave** is intended to support what EF users would call a "database first" scenario. I.e., you already have a database, and you want an easy way to get data in and out of it from a .NET application.
 - It doesn't do any kind of code or schema generation for you: you should already have a schema, and you'll write simple POCO entities from which **Dapper.SimpleSave** will save data to the database. 

## How Dapper.SimpleSave Works?

**Dapper.SimpleSave** saves data to the database through the following processes:

 - Perform a deep comparison between the current object and the previous version of that object, and build a list of differences.
 - Use the list of differences to build a list of operations that describe the changes that need to be made to the corresponding database rows to make them match the new version of the object.
 - Use the list of operations to build a list of commands that will provide the template for the SQL that will be generated - in particular, coalesce updates into a single command for each row.
 - Use the list of commands to build the minimum number of parameterized SQL batches that will update the database with the required changes.
 - Execute the SQL batches sequentially as a single database transaction to update the database, resolving any previously unknown primary key values before each batch is executed.

## Attributes

**Dapper.SimpleSave** needs to decorate your domain classes with the attribute, for example; 

- Names of the tables and columns in a database.
- Which property represents primary key columns. 
- The cardinality of relationships between tables etc.

```csharp
[Table("Invoices")]
public class Invoice
{
    [PrimaryKey]
    public int? Id { get; set; }
    public string Code { get; set; }
    public InvoiceKind Kind { get; set; }

    [OneToMany]
    public List<InvoiceItem> Items { get; set; }

    [OneToOne]
    public InvoiceDetail Detail { get; set; }
}
```

## NuGet Installation

**Dapper.SimpleSave** is only available through NuGet: <a href="https://www.nuget.org/packages/Dapper.SimpleSave/" target="_blank">https://www.nuget.org/packages/Dapper.SimpleSave/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper.SimpleSave 
```

More information can be found at: <a href="https://github.com/Paymentsense/Dapper.SimpleSave/" target="_blank">https://github.com/Paymentsense/Dapper.SimpleSave/</a>

## APIs

Once you installed this library then the following extension methods will automatically add to `DbConnection`:

- Create
- CreateAll
- Update
- UpdateAll
- Delete
- DeleteAll
- SoftDelete
- SoftDeleteAll

You can use these extension methods easily in your code.

```csharp
connection.Create(invoice);

connection.Update(oldInvoice, newInvoice);

connection.Delete(invoice);

connection.SoftDelete(invoice);
```

You can find the detailed documentation here: <a href="https://github.com/Paymentsense/Dapper.SimpleSave/wiki/" target="_blank">https://github.com/Paymentsense/Dapper.SimpleSave/wiki/</a>

## Limitations
Current limitations:
- Convention-based mapping is not supported, and you will need to decorate your domain classes with attributes.
- Currently, only **integer**, **long**, and **GUID** primary keys are supported.
- In all your domain classes, a primary key property should be marked with a [PrimaryKey] attribute.

## Support
Unfortunately, this library is not supported.

## Related Articles

- [Dapper Insert](/execute#example-execute-insert)
- [Dapper Plus Bulk Insert](/bulk-insert)