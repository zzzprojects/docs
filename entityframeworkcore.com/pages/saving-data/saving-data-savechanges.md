---
permaid: 1000250
Title: EF Core SaveChanges - Learn How to Save Entities in a Database
MetaDescription: Unlock the power of EF Core by using SaveChanges to save all entities in a database. Learn how to persist data by adding, updating, and deleting them.
LastMod: 2023-02-24
tags: save savechanges
---

# EF Core SaveChanges: Discover How to Save Entities in a Database

## Introduction

`DbContext.SaveChanges` method saves all changes made in this context to the database. You can add, modify, and remove data using your context and entity classes.

#### Add Data

You can use the `DbSet.Add` method to add new instances of your entity classes, and then when you call SaveChanges, it will insert data into the database.


```csharp
using (var context = new MyContext())
{
    var customer = new Customer 
    { 
        FirstName = "Mark", 
        LastName = "Upston" 
    };

    context.Customers.Add(customer);
    context.SaveChanges();
}
```

#### Updating Data

When you made changes to any existing entity, or simply modify the values assigned to properties and then call SaveChanges, it will update the data into a database.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers.FirstOrDefault();
    customer.LastName = "Andy";
    context.SaveChanges();
}
```

#### Deleting Data

You can use the `DbSet.Remove` method to delete instances of your entity classes. If the entity already exists in the database, it will be deleted during SaveChanges. If the entity has not yet been saved to the database, then it will be removed from the context and will no longer be inserted when SaveChanges is called.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers.FirstOrDefault();
    context.Customers.Remove(customer);
    context.SaveChanges();
}
```
