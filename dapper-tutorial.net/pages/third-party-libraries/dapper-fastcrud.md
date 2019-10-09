---
PermaID: 1000206
Name: FastCrud
---

# Dapper.FastCrud

## Overview
Dapper.FastCrud is an extension for Dapper and is based on C# 6 and VB 14 essential features that have finally raised the simplicity of raw SQL constructs to acceptable maintenance levels. 

It is recommended to use Visual Studio 2015 or an equivalent build environment when using features like string interpolation.

## Installation
Dapper.FastCrud is available through NuGet: <a href="https://www.nuget.org/packages/Dapper.FastCrud" target="_blank">https://www.nuget.org/packages/Dapper.FastCrud</a>

You can easily install this library by running the following command:
```csharp
PM> Install-Package Dapper.FastCrud
```

## Features
- This library package contains both .NET 4.5 and 4.6 DLLs, and one of them will be installed only based on the target framework selected in your project.
- Dapper.FastCrud can support SQLSever, LocalDB, SQLite, MySQL and PostgreSQL etc.
- Composite primary keys mapping. 
- Multiple entity mappings are supported
- Methods are available for CRUD operations.
- A simple SQL builder with alias support is provided, which can be used when manual SQL queries are unavoidable.
- Entity generation support is also provided by installing the NuGet package, Dapper.FastCrud.ModelGenerator.

## APIs
Once you installed this library then the following extension methods will automatically add to DbConnection:

- Get
- Find
- Insert
- Update
- BulkUpdate
- Delete
- BulkDelete
- Count

There is no configuration required and no need for extra coding. You can easily use these extension methods in your code.

```csharp
var invoice = connection.Get(new Invoice { Id = 1 });
var list = connection.Find<Invoice>();

connection.Insert<Invoice>(new Invoice { Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1" });

invoice.Code = "Updated_invoice";
invoice.Kind = InvoiceKind.WebInvoice;

var status = connection.Update<Invoice>(invoice);

var isDeleted = connection.Delete<Invoice>(new Invoice { Id = 2 });

var count = connection.Count<Invoice>();
```

You can find the detailed documentation here: <a href="https://github.com/MoonStorm/Dapper.FastCRUD/wiki" target="_blank">https://github.com/MoonStorm/Dapper.FastCRUD/wiki</a>

## Support
This library is supported on a regular basis and you will get your answers within next few days. <a href="https://github.com/MoonStorm/Dapper.FastCRUD/issues">https://github.com/MoonStorm/Dapper.FastCRUD/issues</a>