---
PermaID: 1000210
Title: Dapper.Rainbow - A Getting Started Tutorial for CRUD Operations
MetaDescription: Discover Dapper.Rainbow Library to perform CRUD operations such as Get, Insert, Update, Delete, All. Learn about Dapper.Rainbow limitations and how to install it with NuGet.
LastMod: 2023-10-23
---

# Dapper.Rainbow: A Getting Started Tutorial for CRUD Operations

## Overview

**Dapper.Rainbow** is a small library that contains an abstract class that you can add to your project and use as a base class for your Dapper classes to provide CRUD operations, such as inserting, deleting, updating, and getting records.

- Get
- Insert
- Update
- Delete
- All

**Dapper.Rainbow** is a wrapper for database interactions and will create SQL based on property names and type constraints.

 - It is built on top of Dapper and makes use of the existing dapper methods. 
 - The main goal is to make working with Dapper even easier by providing a simpler and more consistent API.

## NuGet Installation

**Dapper.Rainbow** is available through NuGet: <a href="https://www.nuget.org/packages/Dapper.Rainbow/" target="_blank">https://www.nuget.org/packages/Dapper.Rainbow/</a>

You can easily install this library by running the following command:
```csharp
PM> Install-Package Dapper.Rainbow
```

More information and documentation can be found at: <a href="https://github.com/StackExchange/Dapper/tree/master/Dapper.Contrib">https://github.com/StackExchange/Dapper/tree/master/Dapper.Contrib</a>

## APIs

Once you installed this library, you will need to create a new class derived from the Database<T> class which will work as a container for all the tables.

```csharp
class MyDatabase : Database<MyDatabase>
{
    public Table<Invoice> Invoices { get; set; }
}
```

You can now use CRUD methods easily in your code.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var db = MyDatabase.Init(connection, commandTimeout: 2);

    int? invoiceId = db.Invoices.Insert(new { Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1" });

    var invoice = db.Invoices.Get((int)invoiceId);

    db.Invoices.Update(invoice.Id, new { Kind = InvoiceKind.StoreInvoice, Code = "Update_Single_1" });

    invoice = db.Invoices.Get(invoice.Id);

    var status = db.Invoices.Delete(invoice.Id);

    var invoices = db.Invoices.All();
}
```

Unfortunately, there is no proper documentation available for this library.

## Limitations

Current limitations:

- There is no support for composite key mapping.
- The identity column name for all tables must be called **Id**.

## Support

This library is supported regularly and you will get your answers within the next few days. <a href="https://github.com/StackExchange/Dapper/issues">https://github.com/StackExchange/Dapper/issues</a>