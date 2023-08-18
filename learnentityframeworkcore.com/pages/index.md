---
title: Entity Framework Core Documentation And Tutorials
description: An introduction to Entity Framework Core and Object Relational Mappers
canonical: /
status: Published
lastmod: 2023-08-15
---

# Welcome To Learn Entity Framework Core

This site provides documentation and tutorials for people looking for help with using EF Core, Microsoft's recommended data access technology for applications based on the [.NET Core framework](https://www.microsoft.com/net/core).

## What is Entity Framework Core / EF Core?

Entity Framework Core (EF Core) is an open-source, lightweight, and cross-platform version of Entity Framework data-access technology. It is an Object-Relational Mapper (ORM) tool that enables developers to work with relational databases using .NET objects. It has been designed to be lightweight, and extensible and to support cross-platform development as part of Microsoft's .NET Core framework. It has also been designed to be simpler to use and to offer performance improvements over previous versions of Entity Framework. 

EF Core is an object-relational mapper (ORM). Object-relational mapping is a technique that enables developers to work with data in an object-oriented way by performing the work required to **map** between **objects** defined in an application's programming language and data stored in **relational** data sources.

 - EF Core provides a set of APIs for managing database operations, such as querying, inserting, updating, and deleting data, and it supports a wide range of relational databases including SQL Server, MySQL, SQLite, and PostgreSQL. 
 - EF Core supports LINQ (Language-Integrated Query), which allows developers to write expressive and efficient queries using C# or Visual Basic.
 - EF Core also provides a variety of performance-related features such as lazy loading, change tracking, and caching. 
 - Additionally, it allows developers to use LINQ (Language Integrated Query) to query and filter data, similar to how you would query an in-memory collection.

## Why use an ORM?

An ORM (Object-Relational Mapper) is used to interact with a database using an object-oriented programming language. ORMs allow developers to work with databases using familiar, object-oriented concepts, rather than writing raw SQL statements. 

 - This can make the development process more efficient and less error-prone, as well as allow for easier maintenance of the codebase. 
 - Additionally, ORMs often provide features such as caching, lazy loading, and connection pooling, which can improve application performance. 
 - They also provide an abstraction layer between the application and the database, so that the application code can be insulated from changes to the underlying database schema. 
 - This can make it easier to switch to a different database in the future or to improve scalability by distributing the data across multiple servers. 
 - Overall, ORMs can help improve developer productivity, code maintainability, and application performance.

Most development frameworks include libraries that enable access to data from relational databases via recordset-like data structures. The following code sample illustrates a typical scenario where data is retrieved from a database and stored in an ADO.NET `DataTable` so that it is accessible to the program's code:


```csharp
using(var conn = new SqlConnection(connectionString))
using(var cmd = new SqlCommand("select * from Products", conn))
{
    var dt = new DataTable();
    using(var da = new SqlDataAdapter(cmd))
    {
        da.Fill(dt);
    }
}
```

The data within the DataTable is accessible via numeric or string indexers and needs to be converted from `object` to the correct type:

```csharp
foreach(DataRow row in dt.Rows)
{
    int productId = Convert.ToInt32(row[0]);
    string productName = row["ProductName"].ToString();
}
```
This late-bound or "weakly-typed" approach to data access is prone to error. Problems commonly arise from mistyping the name of a column, finding that the name of the column has been changed in the database, or from a change to the order in which fields are specified in the SQL statement without a corresponding change being made to the application code. Equally, data type conversions might fail. The code will still compile but will error at runtime. Consequently, professional developers prefer to work with data in a strongly-typed manner. 

## Features

Entity Framework Core (EF Core) has several features that make it a powerful and efficient tool for data access and management:

 - **Cross-platform**: EF Core can be used on a variety of platforms including Windows, Linux, and Mac.
 - **Lightweight**: EF Core has a smaller footprint and fewer dependencies than the full version of Entity Framework.
 - **Code first**: EF Core allows developers to create a database from code, which enables a more agile and test-driven development workflow.
 - **LINQ support**: EF Core supports LINQ, a powerful and expressive query language, which allows developers to write efficient and readable queries using C# or Visual Basic.
 - **Support for multiple databases**: EF Core supports a wide range of relational databases including SQL Server, MySQL, SQLite, and PostgreSQL.
 - **Migrations**: EF Core has built-in support for creating and managing database migrations, which allows for easy management of database changes over time.
 - **Performance improvements**: EF Core has been optimized for performance and can handle large datasets efficiently.
 - **Relationships**: Support for [one-to-one](/conventions/one-to-one-relationship), [one-to-many](/conventions/one-to-many-relationship), and [many-to-many](/conventions/many-to-many-relationship)
 - **Inheritance**: Support for [TPC](/inheritance/table-per-concrete), [TPH](/inheritance/table-per-hierarchy), and [TPT](/inheritance/table-per-type)
 - Support for Client-evaluation and Lazy Loading.
 - Support for explicit loading, change tracking, and caching.

## Strong Typing

Entity Framework Core (EF Core) is an ORM (Object-Relational Mapping) framework for the .NET platform. One of the key features of EF Core is its support for strong typing.

 - When working with EF Core, you define your database schema using C# classes known as entities, these classes are then mapped to the corresponding tables in the database. 
 - Because the entities are strongly typed, the compiler can catch any errors related to the schema at compile-time, rather than at runtime. 
 - Additionally, EF Core supports the use of LINQ (Language-Integrated Query) to query the data in the database, which provides a strongly-typed, expressive, and easy-to-use API for querying data. 
 - This can make it easier for developers to write maintainable and efficient code.

When you take a strongly-typed approach to data, you work with properties of predefined classes that form a [domain model](/model) in an object-oriented way:

```csharp 
public class Product
{
    int ProductId { get; set; }
    string ProductName { get; set; }
}
int productId = myProduct.ProductId;
string productName = myProduct.ProductName;
```

Work still needs to be done to retrieve and map the data from the database to an instance of the domain object. One option is to write your code to manage this. However, as the domain model grows, the amount of code required can grow and will need more and more development time to maintain. This will increase the overall amount of time required to complete an application.

ORMs are pre-written libraries of code that do this work for you. Full-featured ORMs do a lot more too. They can 

- map a domain model to database objects
- create databases and maintain the schema in line with changes to the model
- generate SQL and execute it against the database
- manage transactions
- keep track of objects that have already been retrieved

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="is-ef-core-reliable-and-stable" itemprop="name">Is EF Core reliable and stable?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Yes, EF Core is now very stable since version 3.1

The latest version of EF Core is now the recommended ORM for any new project over EF6.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-version-of-ef-core-is-compatible-with-which-version-of-net" itemprop="name">Which version of EF Core is compatible with which version of .NET?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

| EF Core | .NET Version |
| ------- | ------------ |
| EF Core 8 | .NET 8+ |
| EF Core 7 | .NET 6+ |
| EF Core 6 | .NET 6+ |
| EF Core 5 | .NET 5+, .NET Core 3.1+ |
| EF Core 3.1 | .NET 5+, .NET Framework 4.7.2+ |

NOTE: Only EF Core 3.1 still supports the .NET Framework

We do not recommand to use any version below EF Core 3.1

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-version-of-ef-core-is-still-maintained" itemprop="name">Which version of EF Core is still maintained?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

EF Core support is moving fast. It looks like they have the same [release lifecycle](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core) as .NET

Supported Version:

- EF Core 8
- EF Core 7 (until May 14, 2024)
- EF Core 6 (until November 12, 2024)

Unsupported Version:

- EF Core 5 (support ended on May 10, 2022)
- EF Core 3.1 (support ended on December 13, 2022 but still not marked as legacy on NuGet)
- EF Core 3.0
- EF Core 2.0
- EF Core 1.0

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-popular-third-party-libraries-can-be-used-with-ef-core" itemprop="name">What popular third-party libraries can be used with EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

You can find a list of popular third-party libraries on:

- [Awesome Entity Framework Core](https://github.com/zzzprojects/awesome-entity-framework-core)
- [EF Core - Tools & Extensions](https://learn.microsoft.com/en-us/ef/core/extensions/#entity-framework-extensions)


</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="is-ef-core-better-than-dapper" itemprop="name">Is EF Core better than Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Both ORMs are great and work with any kind of project, from small-scale to massive enterprise-level applications. The answer is 100% a personal / enterprise culture choice.

Some people prefer to write all queries (Dapper) and some people prefer to work with LINQ (EF Core). Both are their pros and cons.

You can learn more on [Dapper vs EF Core](https://www.learndapper.com/dapper-vs-entity-framework)

</div></div>
</details>

</div>

## Related Articles

- [How To Get Entity Framework Core](/efcore/how-to-get)
