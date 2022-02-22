---
PermaID: 1000205
Name: Dapper Extensions Library
---

# DapperExtensions Library

## Overview
DapperExtensions is a small library package which adds basic CRUD operations to Dapper. It also provides a predicate system for more advanced querying scenarios. DapperExtensions will keep your domain classes pure without any attributes or base class inheritance.

## NuGet Installation
DapperExtensions is only available through NuGet: <a href="http://nuget.org/List/Packages/DapperExtensions" target="_blank">http://nuget.org/List/Packages/DapperExtensions</a>

You can easily install this library by running the following command:
```csharp
PM> Install-Package DapperExtensions 
```

More information can be found at: <a href="https://github.com/tmsmith/Dapper-Extensions" target="_blank">https://github.com/tmsmith/Dapper-Extensions</a>

## APIs
Once you installed this library then the following extension methods will automatically add to DbConnection:

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