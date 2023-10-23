---
PermaID: 1000205
Title: DapperExtensions - A Getting Started Tutorial for CRUD Operations
MetaDescription: Discover DapperExtensions Library to perform CRUD operations such as Get, GetList, Insert, Update, Delete, Count. Learn about DapperExtensions limitations and how to install it with NuGet.
LastMod: 2023-02-20
---

# DapperExtensions: A Getting Started Tutorial for CRUD Operations

## Overview

**DapperExtensions** is a small library package that adds basic CRUD operations to Dapper. It also provides a predicate system for more advanced querying scenarios. 

 - **DapperExtensions** will keep your domain classes pure without any attributes or base class inheritance.
 - It provides a set of helper methods for mapping objects to and from database tables, as well as executing common SQL queries.

## Why use DapperExtensions?

If you are using Dapper in your project, then the **DapperExtensions** library can make your life a lot easier. 

 - It provides a set of helper methods that eliminate the need to write boilerplate code when working with Dapper. 
 - In addition, it can help you avoid common SQL injection attacks by automatically parameterizing all your queries.

## NuGet Installation

**DapperExtensions** is only available through NuGet: <a href="https://www.nuget.org/packages/DapperExtensions" target="_blank">https://www.nuget.org/packages/DapperExtensions</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package DapperExtensions 
```

More information can be found at: <a href="https://github.com/tmsmith/Dapper-Extensions" target="_blank">https://github.com/tmsmith/Dapper-Extensions</a>

## APIs

Once you installed this library then the following extension methods will automatically add to `DbConnection`:

- Get
- GetList
- Insert
- Update
- Delete
- Count

You can use these extension methods easily in your code.

```csharp
var invoice = connection.Get<Invoice>(1);
var invoiceList = connection.GetList<Invoice>();
var identity = connection.Insert<Invoice>(new Invoice { Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1" });

invoice.Code = "Updated_invoice";
invoice.Kind = InvoiceKind.WebInvoice;

var status = connection.Update<Invoice>(invoice);

var isDeleted = connection.Delete<Invoice>(connection.Get<Invoice>(2));

var predicate = Predicates.Field<Invoice>(i => i.Kind, Operator.Eq, InvoiceKind.WebInvoice);

var count = connection.Count<Invoice>(predicate);
```

Unfortunately, there is no proper documentation available for this library.

## Support

Currently, this library is not supported.

## Related Articles

- [Dapper Insert](/execute#example-execute-insert)
- [Dapper Plus Bulk Insert](/bulk-insert)
