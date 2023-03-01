---
PermaID: 1000040
Title: Entity Framework Stored Procedure - Learn How to Save Entities
MetaDescription: Unlock the power of Entity Framework by understanding how to use a stored procedure to save your entities. Learn how to insert, update, and delete your entities using a stored procedure.
LastMod: 2023-02-28
Tags: saving stored procedure
---

# Entity Framework Stored Procedure: Discover How to Save Entities

The Entity Framework allows you to use stored procedures to perform predefined logic on database tables. 

 - It can also specify that EF should use your stored procedures for inserting, updating, or deleting entities.
 - Raw SQL queries can be used to execute a stored procedure.

Here is a simple stored procedure, it will insert an author record into a `Authors` table when executed.

```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[CreateAuthor]') AND type in (N'P', N'PC'))

BEGIN
    EXEC dbo.sp_executesql @statement = N'
    CREATE PROCEDURE [dbo].[CreateAuthor]
        @FirstName Varchar(50),
        @LastName Varchar(50),
        @Address Varchar(100)
    AS
    INSERT INTO dbo.Authors(
        [FirstName],
        [LastName],
        [Address]
    )
    VALUES (@FirstName, @LastName, @Address)
    '
END
```

The `ExecuteSqlCommand` method can be used to execute database commands as a string, and it will return the number of affected rows.

```csharp
using (var context = new BookStore())
{			
    int affectedRows = context.Database.ExecuteSqlCommand("CreateAuthor @p0, @p1, @p2",
        parameters: new[] 
        {
            "Mark",
            "Cuban",
            "23 Tsawassen Blvd."
        });
}
```

[Try it](https://dotnetfiddle.net/3fp0mi)