---
title: Learn Entity Framework Core - Getting Started EF Core Tutorial
description: Getting started with Entity Framework Core with tutorials and documentation
canonical: /
status: Published
lastmod: 2024-08-30
---

# What is Entity Framework Core?

[Entity Framework Core](https://learn.microsoft.com/en-us/ef/core/) (EF Core) is the recommended Object-Relational Mapper (ORM) for [.NET](https://dotnet.microsoft.com/en-us/download), formerly known as .NET Core. As the successor to EF6, EF Core has been entirely reengineered and made open source on [GitHub](https://github.com/dotnet/efcore).

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
{
	using(var cmd = new SqlCommand("select * from Products", conn))
	{
		var dt = new DataTable();
		using(var da = new SqlDataAdapter(cmd))
		{
			da.Fill(dt);
		}
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
 - **Support for multiple databases**: EF Core supports a wide range of relational databases including SQL Server, MySQL, MariaDB, SQLite, and PostgreSQL.
 - **Migrations**: EF Core has built-in support for creating and managing database migrations, which allows for easy management of database changes over time.
 - **Performance improvements**: EF Core has been optimized for performance and can handle large datasets efficiently.
 - **Modeling**: Support for [Complex Type](/model/complex-type) and Owned Type.
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

## Related Articles

- [How To Get Entity Framework Core](/efcore/how-to-get)
