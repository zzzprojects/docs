---
permaid: 1000252
Title: EF Core Stored Procedure - Learn How to use SP to Save Entities
MetaDescription: Unlock the power of EF Core by using Stored Procedure to save entities by keeping the logic in your database. Learn how to create stored procedure and map your entities to it when saving them.
LastMod: 2023-02-24
tags: save stored-procedure sql
---

# EF Core Stored Procedure: Discover How to use SP to Save Entities

## Introduction

The Entity Framework allows you to use stored procedures to perform predefined logic on database tables. Raw SQL queries can be used to execute a stored procedure. 

Here is a simple stored procedure, it will insert a customer record into a Customers table when executed.


```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[CreateCustomer]') AND type in (N'P', N'PC'))

BEGIN
    EXEC dbo.sp_executesql @statement = N'
    CREATE PROCEDURE [dbo].[CreateCustomer]
        @FirstName Varchar(50),
        @LastName Varchar(50),
        @Address Varchar(100)
    AS
    INSERT INTO dbo.Customers(
        [FirstName],
        [LastName],
        [Address]
    )
    VALUES (@FirstName, @LastName,@Address)
    '
END
GO
```

The `ExecuteSqlCommand()` method can be used to execute database commands as a string, and it will return the number of affected rows.


```csharp
using (var context = new MyContext())
{
    int affectedRows = context.Database.ExecuteSqlCommand("CreateCustomer @p0, @p1, @p2",
        parameters: new[] 
        {
            "Elizabeth",
            "Lincoln",
            "23 Tsawassen Blvd."
        });
}
```

Similarly, you can execute stored procedures for Update and Delete commands.
