---
title: ExecuteSql - Executing Raw SQL Queries using EF Core
description: The ExecuteSql method in Entity Framework Core is used to execute raw SQL queries against the database and return an integer specifying the number of rows affected by the SQL query.
canonical: /raw-sql/execute-sql
status: Published
lastmod: 2025-07-13
---

# EF Core ExecuteSql

The `DbContext` exposes a `Database` property which includes a method called `ExecuteSql`. This method returns an integer specifying the number of rows affected by the SQL statement passed to it. Valid operations are `INSERT`, `UPDATE`, and `DELETE`. The method is not used for returning entities. 

## Database.ExecuteSql

The `ExecuteSql` method in Entity Framework Core (EF Core) allows you to execute raw SQL statements directly against the database. This method can be useful when you need to perform database operations that are not supported by the LINQ to Entities API, or when you want to perform database-specific operations.

Here's an example of how you can use ExecuteSql in EF Core:

```csharp
using (var context = new MyDbContext())
{
    var affectedRows = context.Database.ExecuteSql($"UPDATE Customers SET Name='Jane' WHERE Name='John'");
}
```
In this example, the `ExecuteSql` method is used to execute an update statement on the `Customers` table. The method returns the number of rows affected by the command, which is stored in the affectedRows variable.

Note that when using `ExecuteSql`, you must construct the SQL statement yourself, so it's important to be careful with the data that you're passing into the command. To prevent SQL injection attacks, it's recommended to use parameterized SQL statements and pass the parameters to the `ExecuteSql` method.

Here's an example of how you can use a parameterized SQL statement with `ExecuteSql`:

```csharp
using (var context = new MyDbContext())
{
    var name = "John";
    var newName = "Jane";

    var affectedRows = context.Database.ExecuteSql("UPDATE Customers SET Name={newName} WHERE Name={name}");
}
```

## Note

 - Before EF Core 7.0, it was sometimes necessary to use the `ExecuteSql` APIs to perform a "bulk update" on the database, as above; this is considerably more efficient than querying for all matching rows and then using `SaveChanges` to modify them. 
 - EF Core 7.0 introduced `ExecuteUpdate` and `ExecuteDelete`, which made it possible to express efficient bulk update operations via LINQ. It's recommended to use those APIs whenever possible, instead of `ExecuteSql`.