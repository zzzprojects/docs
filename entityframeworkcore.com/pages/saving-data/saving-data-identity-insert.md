---
permaid: 1000249
Title: EF Core Identity Insert - Lean How Explicitly  Insert Identity Value
MetaDescription: Unlock the power of EF Core by using Identity Insert to explicitly insert a value in a database. Learn how to use the `SET IDENTITY_INSERT` SQL to allow inserting an explicit value.
LastMod: 2023-02-24
tags: save identity
---

# EF Core Identity Insert: Discover How Explicitly  Insert Identity Value

## Introduction

When you have a primary key field such as Id or CustomerId with IDENTITY switched on it works beautifully you insert data in Entity Framework but is some cases, you might need to insert explicit values into a SQL Server IDENTITY column. To do so, you need to enable IDENTITY_INSERT before calling SaveChanges() manually.

Let's say you want to a create a new customer with explicit CusomterId value. 


```csharp
var customer = new Customer
{
    CustomerId = 100,
    FirstName = "Elizabeth",
    LastName = "Lincoln",
    Address = "23 Tsawassen Blvd."
};
```

But you can't insert a customer with explicit CustomerId value directly, and you will need to turn on the `IDENTITY_INSERT` before calling `SaveChanges()`.


```csharp
using (var context = new MyContext())
{
    var customer = new Customer
    {
        CustomerId = 100,
        FirstName = "Elizabeth",
        LastName = "Lincoln",
        Address = "23 Tsawassen Blvd."
    };

    context.Customers.Add(customer);

    context.Database.OpenConnection();
    try
    {
        context.Database.ExecuteSqlCommand("SET IDENTITY_INSERT dbo.Customers ON");
        context.SaveChanges();
        context.Database.ExecuteSqlCommand("SET IDENTITY_INSERT dbo.Customers OFF");
    }
    finally
    {
        context.Database.CloseConnection();
    }

    string breakS = "";
}
```

Now the explicit CustomerId will be saved in the database, because before calling `SaveChanges()` we have turned on the `IDENTITY_INSERT` and then turned it off.
