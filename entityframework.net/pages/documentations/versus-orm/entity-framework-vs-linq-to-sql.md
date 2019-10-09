---
PermaID: 1000030
Name: Entity Framework vs LINQ2SQL
---

# Entity Framework vs LINQ to SQL

## Entity Framework vs LINQ to SQL? 

When trying to decide between using the Entity Framework and LINQ to SQL as an ORM, what's the difference?

### StackOverflow Related Questions

 - [Entity Framework vs LINQ to SQL](https://stackoverflow.com/questions/8676/entity-framework-vs-linq-to-sql)
 - [Entity Framework VS LINQ to SQL VS ADO.NET with stored procedures?](https://stackoverflow.com/questions/2698151/entity-framework-vs-linq-to-sql-vs-ado-net-with-stored-procedures)

## Answer

Entity framework allows you to query and modify RDBMS like SQL Server, Oracle, DB2, and MySQL, etc., while LINQ to SQL allows you to query and modify only SQL Server database by using LINQ syntax. 

The difference between Entity Framework and LINQ to SQL are as follows;

| Entity Framework                                           | LINQ to SQL                                                    |
|:---------------------------------------------------------- |:---------------------------------------------------------------|
| Allows one-to-one, one-to-many & many-to-many mappings  | It allows only one to one mapping                                 |
| Generates an .edmx files initially and relation is maintained using 3 different files .csdl, .msl and .ssdl  | It generates a .dbml to maintain the relation  |
| Works with various databases like Oracle, DB2, MYSQL, SQL Server, etc. | It can work only with SQL Server                   |
| Supports Code-first, Model-first, Storage-first approaches | Supports only Storage-first approach        
| Supports the complex type.                                 | It doesn't support the complex type.                           |
| Supports TPH (Table Per Hierarchy), TPT (Table Per Type) TPC (Table Per Concrete Class) inheritance | Supports only TPH inheritance
| Allows you to query data using EntitySQL, ObjectContext, DbContext. | It allows you to query data using DataContext.        |
| Provides a loosely coupled approach, code first approach allow you to use Dependency Injection pattern which makes it loosely coupled. | It provides a tightly coupled approach. |
| It can generate a database from a model.                       | It cannot generate a database from a model.                        |
| Application-centric view of a database                     | Database-centric view of a database                            |
| It can be used for rapid application development with RDBMS like SQL Server, Oracle, DB2, and MySQL, etc. | It can be used for rapid application development only with SQL Server. |