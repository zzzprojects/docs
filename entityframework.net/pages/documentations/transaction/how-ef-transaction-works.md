---
PermaID: 1000037
Name: How EF Transaction Works
---

# How EF Transaction Works

## How Entity Framework Transaction Works?  

How Entity Framework Transaction Works and how to use multiple database operations within a single transaction.

### StackOverflow Related Questions

 - [Using Transactions or SaveChanges(false) and AcceptAllChanges()?](https://stackoverflow.com/questions/815586/using-transactions-or-savechangesfalse-and-acceptallchanges)

## Answer

In Entity Framework 6, when you call SaveChanges() to insert, delete, or update data to the database, then entity framework will wrap that operation in a transaction. 

 - SaveChanges automatically starts a transaction and commits or rolls it back.
 - It means the Entity Framework maintains a transaction for the multiple entity inserts, update and delete in a single SaveChanges() method. 
 - When we execute another operation, the Entity Framework creates a new transaction.

In EF 6 and EF Core, you can use multiple SaveChanges within a single transaction as shown below;


```csharp
using (var context = new EntityContext())
{
    context.Database.Log = Console.Write;

    using (DbContextTransaction transaction = context.Database.BeginTransaction())
    {
        try
        {
            List<Customer>  customerList = new List<Customer>()
            {
                new Customer() { Name ="John"},
                new Customer() { Name ="Andy"},
                new Customer() { Name ="Mark"}
            };

            context.Customers.AddRange(customerList);
            context.SaveChanges();
    
            context.Invoices.Add(new Invoice() { Date = DateTime.Now.AddDays(-107), CustomerID = 1});
            context.SaveChanges();
        }
        catch (Exception ex)
        {
            transaction.Rollback();
        }
    }
}
```

[Try it online](https://dotnetfiddle.net/QZspxa)