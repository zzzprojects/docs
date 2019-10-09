---
PermaID: 1000057
Name: Stored Procedure
---

# Stored Procedure

## What is Stored Procedure?

A stored procedure a SQL code that you can save so that the code can be reused over and over again.

 - If you have an SQL query that you write over and over again, save it as a stored procedure, and then call it to execute it.
 - You can also pass parameters to a stored procedure so that the stored procedure can act based on the parameter value(s) that is passed.

## Stored Procedure in Entity Framework

The Entity Framework allows you to use stored procedures in the Entity Data Model.

 - You can use stored procedures to perform predefined logic on database tables.
 - It can also specify that EF should use your stored procedures for inserting, updating, or deleting entities.

Here is a simple stored procedure, it will return all the records from Authors table when executed.

```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[GetAllAuthors]') AND type in (N'P', N'PC'))

BEGIN

   EXEC dbo.sp_executesql @statement = N'
   CREATE PROCEDURE [dbo].[GetAllAuthors]
   AS
   SELECT * FROM dbo.Authors
   '
END
```

In Entity Framework, you can execute stored procedures using `Database.SqlQuery` method.

```csharp
using (var context = new BookStore())
{
    var authors = context.Database
        .SqlQuery<Author>("EXECUTE dbo.GetAllAuthors")
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/HnkGpN)

### Passing Parameters

You can also pass parameters when executing stored procedures. The following stored procedure will return a book record based on `BookId` which is passed as a parameter.

```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[GetBookById]') AND type in (N'P', N'PC'))

BEGIN

   EXEC dbo.sp_executesql @statement = N'
   CREATE PROCEDURE [dbo].[GetBookById]
   @BookId int
   AS
   SELECT * FROM dbo.Books 
   WHERE BookId = @BookId
   '
END
```

When it is executed, It will return a specific book record from Books table based on `BookId = 2` passed as a parameter.

```csharp
using (var context = new BookStore())
{
    var books = context.Database
        .SqlQuery<Book>("EXECUTE dbo.GetBookById 2")
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/JVRdx3)