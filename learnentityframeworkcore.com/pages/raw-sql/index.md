---
title: Executing Raw SQL Queries using Entity Framework Core
description: Entity Framework Core provides mechanisms for executing raw SQL queries including stored procedures directly against the database
canonical: /raw-sql
status: Published
lastmod: 2025-07-13
---

# EF Core Raw SQL Query

Entity Framework Core is an Object-Relational Mapping (ORM) framework for .NET that allows you to interact with your database using C# code instead of writing raw SQL queries. It provides an abstraction over the underlying database so that you can write code that is agnostic to the database management system being used.

However, EF Core also provides mechanisms for executing raw SQL queries directly against the database in circumstances where you cannot use LINQ to represent the query (e.g. a Full-Text Search), if the generated SQL is not efficient enough, if you want to make use of existing stored procedures, or if you just prefer to write your queries in SQL. 

In Entity Framework Core, you can execute raw SQL queries in several ways:

| Method | Description |
| ------ | ----------- |
| [FromSql](/raw-sql/from-sql) | This method returns a DbSet<T> of the specified type T, where T is the model representing the data returned from the query |
| [SqlQuery](/raw-sql/sql-query) | This method returns an IEnumerable<T> of the specified type T, where T is the model representing the data returned from the query. |
| [ExecuteSql](/raw-sql/execute-sql) | This method is used to execute a raw SQL command that does not return any data. It returns the number of rows affected by the command. |

## Limitations

When returning entity types from SQL queries, you must be aware of all the limitations:

 - Your SQL query must return all the properties of the entity type.
 - The column names in the result set must match the column names that properties are mapped to. 
 - The SQL query cannot contain related data. However, in many cases, you can compose on top of the query using the `Include` operator to return related data.