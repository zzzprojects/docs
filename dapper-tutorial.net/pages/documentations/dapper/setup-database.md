---
PermaID: 1000199
Name: setup-database
---

# Setup Database

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
CREATE TABLE [dbo].[OrderDetail] (
    [OrderDetailID] INT IDENTITY (1, 1) NOT NULL,
    [OrderID]       INT NOT NULL,
    [ProductID]     INT NOT NULL,
    [Quantity]      INT NOT NULL,
    CONSTRAINT [PK_dbo.OrderDetail] PRIMARY KEY CLUSTERED ([OrderDetailID] ASC)
);
```

It will add an **OrderDetail** table with the specified columns to the database.  

## Create Model Class

Create an entity or model class of `OrderDetail` type, this entity will be mapped to **OrderDetail** table in the database.

```csharp
public class OrderDetail
{
    public int OrderDetailID { get; set; }
    public int OrderID { get; set; }
    public int ProductID { get; set; }
    public int Quantity { get; set; }
}
```

## Database Operations

Dapper is an ORM library, which extends methods of IDbConnection interface. These extension methods have efficient code to perform insert, update delete and select methods.

```csharp
string sql = "INSERT INTO OrderDetail (OrderID, ProductID, Quantity) Values (10444, 78, 25);";

using (var connection = new SqlConnection(ConfigurationManager.ConnectionStrings["Connection"].ConnectionString))
{
    var affectedRows = connection.Execute(sql);
}
```
