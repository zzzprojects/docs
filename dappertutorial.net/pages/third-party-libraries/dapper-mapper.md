---
PermaID: 1000208
Title: Dapper.Mapper - A Getting Started Tutorial for Mapping
MetaDescription: Discover Dapper.Mapper Library to map with fluent api mapping. Learn about Dapper.Mapper limitations and how to install it with NuGet.
LastMod: 2024-08-30
---

# Dapper.Mapper: A Getting Started Tutorial for Mapping

## Overview

**Dapper.Mapper** is a small library that extends Dapper multi-mapping functionality. It allows you to map a single row to multiple objects. This library automatically figures out the relationships between the returned objects and automatically assigns them.

 - It provides a convenient, fluent API for mapping objects to and from SQL databases.
 - It is built on top of Dapper, and thus benefits from all of its features, including automatic mapping of columns to properties, and support for multiple database types.
 - **Dapper.Mapper** also supports the mapping of nested objects, and provides a comprehensive set of built-in mappers for common .NET types.

## Features

 - Supports multiple database platforms including SQLite, MySQL, MariaDB, and PostgreSQL.
 - Provides a clean, simple API for mapping data to objects.
 - Generates SQL queries automatically, eliminating the need to write tedious and error-prone SQL by hand.

## NuGet Installation

**Dapper.Mapper** is available through NuGet: <a href="https://www.nuget.org/packages/Dapper.Mapper/" target="_blank">https://www.nuget.org/packages/Dapper.Mapper/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper.Mapper
```

## APIs

If you have used Dapper, then you will have an idea of writing mapping explicitly as shown below.

```csharp
var sql = "SELECT * FROM Invoices AS A INNER JOIN InvoiceDetails AS B ON A.InvoiceID = B.InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query<Invoice, InvoiceDetail, Invoice>(
            sql,
            (invoice, invoiceDetail) =>
            {
                invoice.Detail = invoiceDetail;
                return invoice;
            },
            splitOn: "InvoiceID")
        .Distinct()
        .ToList();
}
```

Instead of writing all these explicitly, **Dapper.Mapper** will handle the relationships between the returned objects automatically.

```csharp
var sql = "SELECT * FROM Invoices AS A INNER JOIN InvoiceDetails AS B ON A.InvoiceID = B.InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query<Invoice, InvoiceDetail>(sql);
}
```

Unfortunately, there is no proper documentation available for this library.

## Support

Currently, this library is not supported.
