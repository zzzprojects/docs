---
permaid: 1000241
Title: EF Core Raw SQL Queries - Learn How to Execute Plain Text SQL
MetaDescription: Unlock the power of EF Core by using Raw SQL Queries to execute any plain text sql instead of using LINQ. Learn how to use sql text can help you to improve readability when a LINQ query becomes too much complex.
LastMod: 2023-02-23
tags: query sql
---

# EF Core Raw SQL Queries: Discover How to Execute Plain Text SQL

## Introduction

Entity Framework Core allows you to execute raw SQL queries directly against the database where you cannot use LINQ to represent the query if the generated SQL is not efficient enough. EF Core provides the `DbSet.FromSql()` method to execute raw SQL queries.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .FromSql("SELECT * FROM dbo.Customers")
        .ToList();
}
```

In the above example, the raw SQL query is specified in the `FromSql()` which will return all the records from the Customers table and transform into Customer entities.

#### Passing parameters

As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack. The `DbSet.FromSql()` method also supports parameterized queries using string interpolation syntax in C#.


```csharp
using (var context = new MyContext())
{
     var customers = context.Customers
         .FromSql("Select * from dbo.Customers where LastName = '{0}'", firstName)
         .ToList();
}
```

#### LINQ Operators

You can also use LINQ Operators after a raw query using `FromSql()` method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .FromSql("Select * from dbo.Customers where FirstName = 'Andy'")
        .OrderByDescending(c => c.Invoices.Count)
        .ToList();

}
```

### Limitations

In Entity Framework Core there are some limitaions, so make sure you know them when using raw SQL queries.

 - The SQL query specified in `FromSql()` method must return data for all properties of the entity type.
 - The column names in the result set must match the column names that properties are mapped to. 
 - The SQL query cannot contain related data, you can compose on top of the query using the Include operator to return related data. 
 - The supplied SQL will be treated as a subquery, make sure that the SQL passed should not contain any characters or options that are not valid on a subquery, like a trailing semicolon etc.
 - SQL statements other than SELECT are recognized automatically as non-composable. As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after FromSql are evaluated in-memory.
