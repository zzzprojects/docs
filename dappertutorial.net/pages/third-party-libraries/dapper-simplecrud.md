---
PermaID: 1000211
Title: Dapper.SimpleCRUD - A Getting Started Tutorial for CRUD Operations
MetaDescription: Discover Dapper.SimpleCRUD Library to perform CRUD operations such as Get, GetList, GetListPaged, Insert, Update, Delete, DeleteList, RecordCount. Learn about Dapper.SimpleCRUD limitations and how to install it with NuGet.
LastMod: 2023-10-23
---

# Dapper.SimpleCRUD: A Getting Started Tutorial for CRUD Operations

## Overview

**Dapper.SimpleCRUD** is a small library package that adds basic CRUD operations to Dapper. **Dapper.SimpleCRUD** uses smart defaults without using attributes in your classes, but it can also be overridden as needed.

- The `Id` column is used as a primary key by default, but you can override it with an attribute.
- By default, queries expect that the table name is matching the class name, but you can override it with an attribute.
- Similarly, queries expect that the column name is matching the property name, but you can also override it with an attribute.

**Dapper.SimpleCRUD** is incredibly easy to use. You can perform all CRUD operations in a matter of minutes. The library takes advantage of dynamic programming to make it simple to work with your data.

## NuGet Installation

**Dapper.SimpleCRUD** is only available through NuGet: <a href="https://www.nuget.org/packages/Dapper.SimpleCRUD/" target="_blank">https://www.nuget.org/packages/Dapper.SimpleCRUD/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper.SimpleCRUD 
```

More information can be found at: <a href="https://github.com/ericdc1/Dapper.SimpleCRUD/" target="_blank">https://github.com/ericdc1/Dapper.SimpleCRUD/</a>

## APIs

Once you installed this library, the following extension methods will automatically add to DbConnection:

- Get
- GetList
- GetListPaged
- Insert
- Update
- Delete
- DeleteList
- RecordCount

You can use these extension methods easily in your code.

```csharp
var invoice = connection.Get<Invoice>(1);

var invoiceList = connection.GetList<Invoice>();

var webInvoices = connection.GetList<Invoice>(new { Kind = InvoiceKind.WebInvoice });

var listPaged = connection.GetListPaged<Invoice>(1, 10, "where Code like '%Invoice%'", "Code desc");

var invoiceId = connection.Insert(new Invoice { Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1" });

invoice.Code = "Update_Invoice";
invoice.Kind = InvoiceKind.StoreInvoice;

var id = connection.Update(invoice);

invoice = connection.Get<Invoice>(invoiceId);

var status = connection.Delete(invoice);

var count = connection.RecordCount<Invoice>("where Code like '%Invoice%'");
```

You can find more information and documentation here: <a href="https://github.com/ericdc1/Dapper.SimpleCRUD/" target="_blank">https://github.com/ericdc1/Dapper.SimpleCRUD</a>

## Support

This library is supported regularly and you will get your answers within the next few days. <a href="https://github.com/ericdc1/Dapper.SimpleCRUD/issues/">https://github.com/ericdc1/Dapper.SimpleCRUD/issues</a>

## Related Articles

- [Dapper Insert](/execute#example-execute-insert)
- [Dapper Plus Bulk Insert](/bulk-insert)