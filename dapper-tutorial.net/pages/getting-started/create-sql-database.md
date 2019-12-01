---
PermaID: 1000199
Name: create-sql-database
---

# Create SQL Database

To perform CRUD operations in the database, we must establish a connection with the database using a database connection. 

## Create Database

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

```cshar
CREATE TABLE [dbo].[Customers] (
    [CustomerID] INT            IDENTITY (1, 1) NOT NULL,
    [FirstName]  NVARCHAR (MAX) NULL,
    [LastName]   NVARCHAR (MAX) NULL,
    [Email]      NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_dbo.Customers] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
);
```

It will add an **Customers** table with the specified columns to the database.  

## Create Model Class

Create an entity or model class of `Customer` type, this entity will be mapped to **Customers** table in the database.

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
}
```

## Database Operations

Dapper is an ORM library, which extends methods of `IDbConnection` interface. These extension methods have efficient code to perform insert, update delete and select methods.

```csharp
string sql = "INSERT INTO Customers (FirstName, LastName, Email) Values ('Carson', 'Alexander', 'carson.alexander@example.com');";

using (var connection = new SqlConnection(ConfigurationManager.ConnectionStrings["Connection"].ConnectionString))
{
    var affectedRows = connection.Execute(sql);
}
```
