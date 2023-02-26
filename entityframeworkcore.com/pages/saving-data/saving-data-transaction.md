---
permaid: 1000253
Title: EF Core Transaction - Learn How to Manage a Transaction When Saving
MetaDescription: Unlock the power of EF Core by using Transaction to handle the rollback logic when an error happens. Learn what a transaction is, how to start it, and to commit it when the save what as success or to rollback it.
LastMod: 2023-02-24
tags: save transaction
---

# EF Core Transaction: Discover How to Manage a Transaction When Saving

## Introduction

In Entity Framework, when you call SaveChanges() to insert, delete, or update data to the database, the entity framework will wrap that operation in a transaction.

 - Transactions allow several database operations to be processed in an atomic manner. 
 - If the transaction is committed, all of the operations are successfully applied to the database. 
 - If the transaction is rolled back, none of the operations are applied to the database.

In EF 6 and EF Core, you can use multiple SaveChanges within a single transaction. You can use the `DbContext.Database` API to begin, commit, and rollback transactions. 

The following example shows two SaveChanges() operations and a LINQ query being executed in a single transaction.


```csharp
using (var context = new MyContext())
{
    using (var transaction = context.Database.BeginTransaction())
    {
        try
        {
            var customer = context.Customers
                .Where(c => c.CustomerId == 2)
                .FirstOrDefault();
                customer.Address = "43 rue St. Laurent";

            context.SaveChanges();
             
            var newCustomer = new Customer
            {
                FirstName = "Elizabeth",
                LastName = "Lincoln",
                Address = "23 Tsawassen Blvd."
            };
             
            context.Customers.Add(newCustomer);
            context.SaveChanges();

            transaction.Commit();
        }
        catch (Exception)
        {
            transaction.Rollback();
        }
    }
}
```

Read more: [Transactions](https://docs.microsoft.com/en-us/ef/core/saving/transactions)
