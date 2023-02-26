---
permaid: 1000242
Title: EF Core Stored Procedure - Learn How to Query a SP in LINQ
MetaDescription: Unlock the power of EF Core by using Stored Procedure in your queries to add more flexibility. Learn how to keep some logic in your database by querying a stored procedure instead of creating a complex LINQ query.
LastMod: 2023-02-23
tags: query stored-procedure sql
---

# EF Core Stored Procedure: Discover How to Query a SP in LINQ

The Entity Framework allows you to use stored procedures to perform predefined logic on database tables. Raw SQL queries can be used to execute a stored procedure. 

Here is a simple stored procedure, it will return all the records from Customers table when executed.


```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[GetAllCustomers]') AND type in (N'P', N'PC'))

BEGIN

   EXEC dbo.sp_executesql @statement = N'
   CREATE PROCEDURE [dbo].[GetAllCustomers]
   AS
   SELECT * FROM dbo.Customers
   '
END
GO
```

In EF Core, you can execute stored procedures using `FromSql()` method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .FromSql("EXECUTE dbo.GetAllCustomers")
        .ToList();
}
```

You can also pass parameters when executing stored procedures.


```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[GetCustomer]') AND type in (N'P', N'PC'))

BEGIN

   EXEC dbo.sp_executesql @statement = N'
   CREATE PROCEDURE [dbo].[GetCustomer]
   @CustomerID int
   AS
   SELECT * FROM dbo.Customers 
   WHERE CustomerID = @CustomerID
   '
END
GO
```

It will return a specific record from Customers table based on CustomerID passed as a parameter.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .FromSql("EXECUTE dbo.GetCustomer 1")
        .ToList();
}
```

You can also pass a parameter value using C# string interpolation.


```csharp
using (var context = new MyContext())
{
    int customerId = 1;
    var customer = context.Customers
        .FromSql($"EXECUTE dbo.GetCustomer {customerId}")
        .ToList();
}
```
