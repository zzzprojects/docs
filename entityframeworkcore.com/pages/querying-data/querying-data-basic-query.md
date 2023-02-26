---
permaid: 1000232
Title: EF Core Query - Learn the Basics about Querying a Database in LINQ
MetaDescription: Unlock the power of EF Core by understanding how queries in LINQ work to understand the basics and more advanced concepts. Learn how to use LINQ and all possibilities it offers.
LastMod: 2023-02-23
tags: query getting-started
---

# EF Core Query: Discover the Basics about Querying a Database in LINQ

Entity Framework Core uses Language Integrate Query (LINQ) to query data from the database. 

 - LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.
 - Querying in Entity Framework Core remains the same as in EF 6 to load entities from the database. 
 - It provides more optimized SQL queries and the ability to include C#/VB.NET functions into LINQ-to-Entities queries.

Let's say we have a simple model which contains three entities.

```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public virtual List<Invoice> Invoices { get; set; }
}
```

## Load All data

The following example load all the data from `Customers` table.

```csharp
using (var context = new MyContext())
{
    var customers = context.Customers.ToList();
}
```

## Load a Single Entity

The following example load a single record from `Customers` table based on `CustomerId`.

```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .Single(c => c.CustomerId == 1);
}
```

## Filter Data

The following example loads all customers with `FirstName` **Mark**.

```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .Where(c => c.FirstName == "Mark")
        .ToList();
}
```
