---
Title: Try Dapper Plus Online with .NET Fiddle
MetaDescription: Learn how to try Dapper Plus online with .NET Fiddle, create tables, run bulk operations, and experiment without configuring a local database.
LastMod: 2026-07-13
---

# Try Dapper Plus Online

You can try Dapper Plus directly in your browser with [.NET Fiddle](https://dotnetfiddle.net/).

While learning Dapper Plus, there is no need to create a project, manually install the NuGet packages, configure a local database, or write a SQL statement to create your table.

Simply add the following directives at the top of your code:

```csharp
// @nuget: Dapper
// @nuget: Microsoft.Data.SqlClient
// @nuget: Z.Dapper.Plus
```

Then add the following `using` directives:

```csharp
using Dapper;
using Microsoft.Data.SqlClient;
using Z.Dapper.Plus;
```

Dapper Plus requires a database connection to execute an operation. You can connect to the SQL Server database provided by .NET Fiddle with the following code:

```csharp
using var connection = new SqlConnection(
    FiddleHelper.GetConnectionStringSqlServer());
```

You can then use the `CreateTable` method to quickly create a table from your entity type:

```csharp
connection.CreateTable<Customer>();
```

The table is automatically created based on the properties and data annotations of the `Customer` class, so you do not need to write a `CREATE TABLE` statement.

For more information about creating tables from entity types, see [Create Table](/create-table).

.NET Fiddle automatically downloads the required NuGet packages before compiling your code.

Everything runs directly in your browser, so you can start experimenting in just a few seconds.

## Try a Basic Bulk Insert

The following example shows how to create a table and insert multiple customers with the `BulkInsert` method:

```csharp
// @nuget: Dapper
// @nuget: Microsoft.Data.SqlClient
// @nuget: Z.Dapper.Plus

using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using Dapper;
using Microsoft.Data.SqlClient;
using Z.Dapper.Plus;

public class Program
{
    public static void Main()
    {
        var customers = new List<Customer>
        {
            new Customer { Name = "Alice", Email = "alice@example.com" },
            new Customer { Name = "Bob", Email = "bob@example.com" }
        };

        using var connection = new SqlConnection(
            FiddleHelper.GetConnectionStringSqlServer());

        // Create the table from the Customer class
        connection.CreateTable<Customer>();

        // Insert all customers
        connection.BulkInsert(customers);

        Console.WriteLine($"{customers.Count} customers inserted.");
        FiddleHelper.WriteTable(connection.Query<Customer>("SELECT * FROM Customer"));
    }
}

[Table("Customer")]
public class Customer
{
    [Key]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public int CustomerID { get; set; }

    public string Name { get; set; }

    public string Email { get; set; }
}
```

[Online Example](https://dotnetfiddle.net/G5DV6V)

The `[Table]`, `[Key]`, and `[DatabaseGenerated]` attributes configure the table name, primary key, and identity column created by the `CreateTable` method.

You can click the **Online Example** link to open and run the example directly in .NET Fiddle.

Alternatively, you can copy the code into a new .NET Fiddle.

You can then modify the customers, their values, or the bulk operation to experiment with your own scenarios.

## Try Any Documentation Example

You can use the same approach with almost any example from this documentation:

- Open .NET Fiddle.
- Add the required NuGet directives at the top of the code.
- Copy the example you want to try.
- Add any required `using` directives.
- Connect to the SQL Server database provided by .NET Fiddle.
- Create any required tables with the `CreateTable` method.
- Click **Run**.

```csharp
// @nuget: Dapper
// @nuget: Microsoft.Data.SqlClient
// @nuget: Z.Dapper.Plus

using Dapper;
using Microsoft.Data.SqlClient;
using Z.Dapper.Plus;

using var connection = new SqlConnection(
    FiddleHelper.GetConnectionStringSqlServer());

connection.CreateTable<Customer>();
```

Some examples may require additional NuGet packages, tables, mappings, or setup code. The **Online Example** links in our documentation already include everything required to run them.

This is often the fastest way to:

- Test a bulk operation.
- Explore a feature.
- Try different options.
- Reproduce an issue.
- Compare different approaches.
- Share an example with someone else.

## More Online Examples

If you want to explore more features, you can browse our collection of online examples.

Every example is ready to run and edit in .NET Fiddle, making it easy to learn new features, experiment with different options, or adapt the code to your own scenarios.

[Browse All Online Examples](/online-examples)

## Summary

In this article, you learned how to use .NET Fiddle to try Dapper Plus without creating a project, manually installing the required NuGet packages, or configuring a local database.

Continue with the following articles to learn more about Dapper Plus:

- [Bulk Insert](/bulk-insert)
- [Bulk Update](/bulk-update)
- [Bulk Delete](/bulk-delete)
- [Bulk Merge](/bulk-merge)