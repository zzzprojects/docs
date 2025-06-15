---
title: Welcome To Learn Dapper ORM - A Dapper Tutorial for C# and .NET Core
description: Dapper is a simple and efficient .NET library for data access and object-relational mapping (ORM) that supports .NET Core and C# language.
keywords: dapper c#, dapper in c#, dapper nuget, what is dapper, what is dapper in .net core
canonical: /
status: Published
lastmod: 2025-06-15
---

# Welcome to Learn Dapper

This site is for developers who want to learn how to use Dapper - the micro ORM **downloaded over 350m times** and created by the people behind [Stack Overflow](https://stackoverflow.com/questions/tagged/dapper).

Through this tutorial, you will also learn about Dapper Plus, a third-party library that offers features like [Create Table](https://dapper-plus.net/create-table) and [Bulk Extensions (such as BulkInsert)](https://dapper-plus.net/bulk-extensions-methods) methods.

## What is Dapper?

Dapper is an open-source object-relational mapping (ORM) library for .NET and .NET Core applications. The library allows developers to quickly and easily access data from databases without the need to write tedious code. Dapper allows you to [execute raw SQL queries](/non-query), map the results to objects, and [execute stored procedures](/stored-procedures), among other things. It is available as a [NuGet package](https://dappertutorial.net/download).

 - Dapper is lightweight and fast, making it an ideal choice for applications that require low latency and high performance. 
 - It is a simple yet powerful object mapping tool for any .NET language, such as C#, that enables developers to quickly and easily map query results from ADO.NET data readers to instances of business objects. 
 - It has excellent support for both [asynchronous and synchronous database queries](/dapper-query/index) and [batching multiple queries](/dapper-query/selecting-multiple-results) together into a single call. 
 - Additionally, Dapper supports [parameterized queries](/parameters) to help protect against SQL injection attacks.

## Does Dapper support .NET Core?

Yes, Dapper has been supporting .NET Core since the release of version 1.50 in July 2016. Dapper is a cross-platform .NET library, which means that it can be used on any platform that supports .NET, including .NET Core.

## Does Dapper support C#?

Yes, you can use Dapper with C# as multiple other languages, such as VB.NET and F#. In fact, Dapper was written in C# and is a popular choice for data access in C# applications because of its simplicity and efficiency. 

## Is Dapper an ORM?

Dapper falls into a family of tools known as _micro-ORMs_. These tools perform only a subset of the functionality of full-blown Object Relations Mappers, such as [Entity Framework Core](https://www.learnentityframeworkcore.com), but Dapper is known for its speed and simple implementation compared to others. The following table provides a general idea of the capabilities that you can expect to find in a micro ORM compared to an ORM:

| | Micro ORM | ORM|
|:--|:--|:--|
| Map queries to objects  | ✅  | ✅ |
| Caching results    | ❌ | ✅|
| Change tracking  | ❌ <sup>1</sup> | ✅|
| SQL generation    | ❌ <sup>2</sup> | ✅|
| Identity management | ❌ | ✅|
| Association management | ❌ | ✅|
| Lazy loading | ❌ | ✅|
| Unit of work support | ❌ | ✅|
| Database migrations | ❌ | ✅|
| | | |

Dapper concentrates its efforts on the **O** and **M** of _ORM_ - **O**bject **M**apping.

[1] Some extensions have been added to Dapper that provide the minimal change-tracking capability

[2] Dapper does generate SQL but in a limited fashion. Third-party libraries such as [Dapper Plus](https://dapper-plus.net/) can generate the full SQL for [insert](/bulk-operations/bulk-insert), [update](/bulk-operations/bulk-update), and [delete](/bulk-operations/bulk-delete) statements

## When Should You Use Dapper?

When deciding whether to use Dapper or not, one should bear in mind the primary reason for its existence - performance. The original developers of Dapper were using Entity Framework Core's predecessor - the short-lived [Linq to SQL](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/linq/). However, they found that query performance wasn't good enough for the increasing traffic that the site in question (Stack Overflow) was experiencing, [so they wrote their own micro ORM](https://samsaffron.com/archive/2011/03/30/How+I+learned+to+stop+worrying+and+write+my+own+ORM). For a comparison, see our [Dapper vs Entity Framework (EF Core)](/dapper-vs-entity-framework) article.

Dapper is, therefore, a good choice in scenarios where read-only data changes frequently and is requested often. It is particularly good in stateless scenarios (e.g. the web) where there is no need to persist complex object graphs in memory for any duration.

Dapper does not translate queries written in .NET languages to SQL like a full-blown ORM. So you need to be comfortable writing queries in SQL or have someone write them for you. 

Dapper has no real expectations about the schema of your database. It is not reliant on conventions in the same way as [Entity Framework Core](https://entityframeworkcore.com/), so Dapper is also a good choice where the database structure isn't particularly normalized.  

Dapper works with an ADO.NET `IDbConnection` object, which means that it will work with any database system for which there is an ADO.NET provider.

There is no reason why you cannot use both an ORM and a micro ORM in the same project.  

## What does Dapper do?

Here is a standard ADO.NET C# code for retrieving data from a database and materializing it as a collection of `Product` objects:

```csharp
var sql = "SELECT * FROM products";
var products = new List<Product>();
using (var connection = new SqlConnection(connString))
{
    connection.Open();
    using (var command = new SqlCommand(sql, connection))
    {
        using (var reader = command.ExecuteReader())
        {
            var product = new Product
            {
                ProductID = reader.GetInt32(reader.GetOrdinal("ProductID")),
                ProductName = reader.GetString(reader.GetOrdinal("ProductName")),
                SupplierID = reader.GetInt32(reader.GetOrdinal("SupplierID")),
                CategoryID = reader.GetInt32(reader.GetOrdinal("CategoryID")),
                UnitPrice = reader.GetDecimal(reader.GetOrdinal("UnitPrice")),
                UnitsInStock = reader.GetInt32(reader.GetOrdinal("UnitsInStock")),
                UnitsOnOrder = reader.GetInt32(reader.GetOrdinal("UnitsOnOrder")),
                Discontinued = reader.GetBoolean(reader.GetOrdinal("Discontinued")),
                DiscontinuedDate = reader.GetDateTime(reader.GetOrdinal("DiscontinuedDate"))
            };
            products.Add(product);
        }
    }
}
```

At its most basic level, Dapper replaces the highlighted block of assignment code in the example above with the following:

```csharp
products = connection.Query<Product>(sql).ToList();
```

[Online Example](https://dotnetfiddle.net/2aFqUE)

Dapper also creates the command and opens the connection if needed. If you use Dapper to manage basic assignments like this, it will save you hours. However, Dapper is capable of doing quite a bit more.

### Where can you get Dapper?

Dapper is available from [NuGet](https://dappertutorial.net/download). It is compliant with .NET Standard 2.0, which means you can use it in .NET applications that target the .NET Framework and .NET Core. You can install the latest version using the following command with the .NET CLI:

```cmd
dotnet add package Dapper
```
Or run the following command to install the **Dapper NuGet** package in the Package Manager Console:

```csharp
PM> Install-Package Dapper
```

The source code for Dapper [is available on GitHub](https://github.com/DapperLib/Dapper). To clone the Dapper GitHub repository, run the following command in a Git Bash window:

```csharp
git clone https://github.com/StackExchange/Dapper.git
```

## Conclusion

In conclusion, Dapper is an excellent choice for applications that require simple and efficient data access. It is easy to use, lightweight and performant, making it a perfect option for developers who need fast data access without the overhead of more complex ORM solutions. Furthermore, Dapper is compatible with .NET Framework and .NET Core, and you can use it with any .NET language, including C#, VB.NET, and F#. Whether you are new to ORMs or have experience with other libraries, Dapper is a powerful tool worth considering for your data access needs. Dapper is enriched by third-party libraries such as [Dapper Plus](https://dapper-plus.net/), which aims to provide additional features, including bulk extension methods.