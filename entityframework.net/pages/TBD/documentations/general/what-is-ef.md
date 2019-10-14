---
PermaID: 1000094
Name: What's Entity Framework
---

# What is Entity Framework?

Entity Framework (EF) is an open source ORM framework for .NET applications supported by Microsoft. EF simplifies mapping between objects in your software to the tables and columns of a relational database.

 - It enables developers to work with data using objects of domain-specific classes without focusing on the underlying database tables and columns where this data is stored.
 - It takes care of creating database connections and executing commands, as well as taking query results and automatically materializing those results as your application objects.
 - It also helps to keep track of changes to those objects, and when instructed, it will also persist those changes back to the database for you.

## Why Entity Framework?

#### Easy To Maintain

 - You can write code against the Entity Framework, and the system will automatically produce objects for you as well as track changes on those objects and simplify the process of updating the database. 
 - The EF can, therefore, replace a large chunk of code you would otherwise have to write and maintain yourself. 

#### Abstraction

 - The mapping between your objects and your database is specified declaratively instead of in code. 
 - If you need to change your database schema, you can minimize the impact on the code you have to modify in your applications.
 - It provides a level of abstraction which helps isolate the app from the database.

#### Easy to Use

 - The queries and other operations you write into your code are specified in a syntax that is not specific to any particular database.
 - With the EF, the queries are written in LINQ or Entity SQL and then translated at runtime by the providers to the specific back-end query syntax for that database.

## Features

 - Mapping of POCO entity classes which do not depend on any EF types
 - Automatic change tracking
 - Identity resolution and Unit of Work
 - Eager, lazy and explicit loading
 - Translation of strongly-typed queries using LINQ (Language INtegrated Query)
 - Rich mapping capabilities, including support for:
   - One-to-one, one-to-many and many-to-many relationships
   - Inheritance (table per hierarchy, table per type and table per concrete class)
   - Complex types
   - Stored procedures
 - A visual designer to create entity models.
 - A "Code First" experience to create entity models by writing code.
 - Models can either be generated from existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.
 - Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.
 - Database connectivity based on ADO.NET and numerous providers available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.