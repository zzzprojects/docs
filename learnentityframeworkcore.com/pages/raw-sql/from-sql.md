---
title: Executing Raw SQL Queries using FromSql Method
description: The FromSql method in Entity Framework Core is used to execute raw SQL queries against the database and return the results as entities. It allows you to bypass the LINQ query pipeline and execute custom SQL.
canonical: /raw-sql/from-sql
status: Published
lastmod: 2025-07-13
---

# EF Core FromSql

## DbSet.FromSql

The `FromSql` method in Entity Framework Core allows you to execute a raw SQL query and map the results to entities. It's used to retrieve data from a database using custom SQL and map it directly to a type that represents the data. 

 - The `FromSql` method is an extension method on the `DbSet` class and takes a raw SQL query string and an array of parameters as arguments. 
 - The results of the query are then materialized into entity objects and returned as an `IQueryable` which can be further manipulated or enumerated.
 - The `FromSql` was introduced in EF Core 7.0, if you are using older versions of EF Core, make sure to use `FromSqlInterpolated` instead.

Here's an example of how to use the `FromSql` method in Entity Framework Core:

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
    public int AuthorId{ get; set; }
    public string ISBN { get; set; }
}

...

public class LibraryContext : DbContext
{
    public DbSet<Book> Books { get; set; }
}
...
```
```csharp
using (var context = new LibraryContext())
{
    FormattableString sql = $"SELECT * FROM Books WHERE Title = 'Hamlet'";
    var book = context.Books.FromSql(sql).FirstOrDefault();
}
```

In the example, `LibraryContext` is your database context class, `Books` is the entity set for the `Books` table, and the `FromSql` method is used to execute the raw SQL query and retrieve the result.

It's important to note that when using the `FromSql` method, you need to make sure that the column names and data types in the raw SQL query match the properties and data types of the entity you want to return.

### DbSet.FromSql Parameterized Queries

In Entity Framework Core, you can pass parameters to a raw SQL query executed using the `FromSql` method. This helps to prevent SQL injection attacks and makes the code more readable.

The following example passes a single parameter to a SQL query by including a parameter placeholder in the SQL query string and providing an additional argument:

```csharp
using (var context = new LibraryContext())
{
    var title = "Hamlet";
    var book = context.Books.FromSql($"SELECT * FROM Books WHERE Title = {title}").FirstOrDefault();
}
```

## DbSet.FromSql Stored Procedures

The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument:

```csharp
var author = "Shakespeare";

var books = context.Books
    .FromSql($"EXECUTE dbo.GetMostPopularBooks {author}")
    .ToList();
```

While this syntax may look like regular C# string interpolation, the supplied value is wrapped in a `DbParameter` and the generated parameter is specified in a placeholder. This makes `FromSql` safe from SQL injection attacks, and sends the value efficiently and correctly to the database.

If you need more control over the database parameter being sent, you can also construct a `DbParameter` and supply it as a parameter value. This allows you to set the precise database type of the parameter, or facets such as its size, precision, or length:

```csharp
var author = new SqlParameter("author", "Shakespeare");

var books = context.Books
    .FromSql($"EXECUTE dbo.GetMostPopularBooks {author}")
    .ToList();
```

## DbSet.FromSqlRaw

Wherever feasible, `FromSql` and its parameterization should be utilized. However, there are some circumstances where dynamic SQL is required and the usage of database parameters is not an option. 

So, in this case, you'll have to use `FromSqlRaw`, which allows interpolating variable data directly into the SQL string, instead of using a database parameter.

The `DbSet.FromSqlRaw` method (`DbSet.FromSql` before Entity Framework Core 3.0) enables you to pass in a SQL command to be executed against the database to return instances of the type represented by the `DbSet`:

```csharp
using (var context = new SampleContext())
{
    var books = context.Books.FromSqlRaw("SELECT BookId, Title, AuthorId, ISBN FROM Books").ToList();
}
```
The `DbSet` must be included in the model (i.e. it cannot be configured as `Ignored`). All columns in the target table that map to properties on the entity must be included in the SQL statement. The column names must match those that the properties are mapped to. Property names are not taken into account when the results are hydrated into instances of the entity. 

If any columns are missing, or are returned with names not mapped to properties, an `InvalidOperationException` will be raised with the message: 
> 'The required column '[name of first missing column]' was not present in the results of a 'FromSqlRaw' operation.'



### DbSet.FromSqlRaw Parameterized Queries

You are always advised to parameterize user input to prevent the possibility of a SQL injection attack being successful. Entity Framework Core will parameterize SQL if you use format strings with `FromSqlRaw` or string interpolation with the `FromSqlInterpolated` method:

```csharp
// Format string
var author = db.Authors.FromSqlRaw("SELECT * From Authors Where AuthorId = {0}", id).FirstOrDefault();

// String interpolation
var author = db.Authors.FromSqlInterpolated($"SELECT * From Authors Where AuthorId = {id}").FirstOrDefault();
```

Both of these approaches result in the following SQL being generated (for SQLite):

```sql
SELECT "a"."AuthorId", "a"."FirstName", "a"."LastName"
FROM (
    SELECT * From Authors Where AuthorId = @p0
) AS "a"
LIMIT 1
```

:::{.alert .alert-danger}
Entity Framework Core will only parameterize format strings if they are supplied inline with the `FromSqlRaw` method call. Format strings declared outside of the `FromSqlRaw` method call will not be parsed for parameter placeholders. In effect, you will be passing a concatenated string directly to the database, which is a SQL injection risk.

The following example is dangerous and should not be used:

```
var sql = string.Format("SELECT * From Authors Where AuthorId = {0}", id);
var author = db.Authors.FromSqlRaw(sql).FirstOrDefault(); 
```

The generated SQL is unparameterized:
```sql
SELECT "a"."AuthorId", "a"."FirstName", "a"."LastName"
FROM (
  SELECT * From Authors Where AuthorId = 2
) AS "a"
LIMIT 1

```
Entity Framework Core includes an analyzer that will warn you if you try to adopt this pattern:

![Entity Framework Core SQL Injection](/images/25-09-2018-10-00-16.png)


:::


You can also explicitly create `DbParameter` objects for the provider that you are using. The first example demonstrates parameter construction for SQLite, and the second for SQL Server:

```csharp
var p1 = new SqliteParameter("@Id", id);
var author = db.Authors.FromSqlRaw($"SELECT * From Authors Where AuthorId = @Id", p1).FirstOrDefault();

var p1 = new SqlParameter("@Id", id);
var author = db.Authors.FromSqlRaw($"SELECT * From Authors Where AuthorId = @Id", p1).FirstOrDefault();
```

## DbSet.FromSqlRaw Stored Procedures

The SQL command can be any valid SQL statement that returns all the required fields of data. It is possible to call stored procedures via the `FromSqlRaw` method:

```csharp
using (var context = new SampleContext())
{
    var books = context.Books
        .FromSqlRaw("EXEC GetAllBooks")
        .ToList();
}
```
It is also possible to pass in values to named parameters:
```csharp
using (var context = new SampleContext())
{
    var authorId = new SqlParameter("@AuthorId", 1);
    var books = context.Books
        .FromSqlRaw("EXEC GetBooksByAuthor @AuthorId" , authorId)
        .ToList();
}
```

## Non-Entity Types and Projections

In versions of EF Core before 2.1, it is not possible to use the `FromSqlRaw` method to return a subset of properties (a projection) directly from the database. Using the Books `DbSet` above as an example, the following will not work:

```csharp
using(var context = new SampleContext())
{
    var books = context.Books.FromSqlRaw("SELECT BookId, Title FROM Books").ToList();
}
```
You must project the result of the `FromSqlRaw` method call to return a subset of properties:

```csharp
using(var context = new SampleContext())
{
    var books = context.Books
        .FromSql("SELECT * FROM Books")
        .Select(b => new {
            BookId = b.BookId,
            Title = b.Title 
            }).ToList();
}
```
However, this may prove inefficient as all columns from the mapped table will be returned by the `FromSql` method call. 

Support for returning ad hoc (not `DbSet`) types from direct SQL calls is possible from EF Core 2.1 using [query types](/query-types).
