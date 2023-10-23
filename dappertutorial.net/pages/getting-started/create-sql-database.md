---
PermaID: 1000199
Title: Dapper - Getting Started With Your First SQL Database
MetaDescription: Learn how to create a SQL database and table to get started with Dapper. Follow our step-by-step Dapper tutorial with to create your first database.
LastMod: 2023-10-22
---

# Dapper: Getting Started With Your First SQL Database

To perform CRUD operations in the database, we must establish a connection with the database using a database connection. 

## Create a Database

The first step is to create a database using the following script.

```csharp
IF NOT EXISTS(SELECT * FROM sys.databases WHERE name = 'mydb')
  BEGIN
    CREATE DATABASE mydb
  END
```

This script will add a database named **mydb** if it doesn't exist.

## Create Table

Once the database is created, you can now create a table in the database.

```csharp
CREATE TABLE [dbo].[Customers] (
    [CustomerID] INT            IDENTITY (1, 1) NOT NULL,
    [FirstName]  NVARCHAR (MAX) NULL,
    [LastName]   NVARCHAR (MAX) NULL,
    [Email]      NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_dbo.Customers] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
);
```

It will add a **Customers** table with the specified columns to the database. 

You can also use the [Dapper Plus - CreateTable](https://dapper-plus.net/create-table) extensions for quick tests.

## Create Model Class

Create an entity or model class of `Customer` type, this entity will be mapped to the **Customers** table in the database.

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
}
```

You can also use [Dapper Entity to Table Converter](https://zzzcode.ai/dapper/table-to-entity-converter) to automatically convert your table to entity.

## Database Operations

Dapper is an ORM library, which extends methods of the `IDbConnection` interface. These extension methods have efficient code to perform insert, update delete, and select methods.

```csharp
string sql = "INSERT INTO Customers (FirstName, LastName, Email) Values ('Carson', 'Alexander', 'carson.alexander@example.com');";

using (var connection = new SqlConnection(ConfigurationManager.ConnectionStrings["Connection"].ConnectionString))
{
    var affectedRows = connection.Execute(sql);
}
```

In this example, the [Dapper Execute](/execute#example-execute-insert) extension method has been used to insert a new customer.

## Related Articles

- [Dapper Execute](/execute#example-execute-insert)
- [Dapper Plus - CreateTable](https://dapper-plus.net/create-table)
- [Dapper AI - Entity to Table Converter](https://zzzcode.ai/dapper/table-to-entity-converter)