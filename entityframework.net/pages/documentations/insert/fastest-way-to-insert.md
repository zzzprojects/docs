---
PermaID: 1000086
Name: Fastest Way to Insert
---

# Fastest Way To Insert

## What's the fastest way to insert?

When you want to insert hundreds, thousands, or millions of entities, and your application suffers from performances issues.

### StackOverflow Related Questions

 - [Fastest Way of Inserting in Entity Framework](https://stackoverflow.com/questions/5940225/fastest-way-of-inserting-in-entity-framework)
 - [Writing to large number of records take too much time using Entity Framework](https://stackoverflow.com/questions/43981993/writing-to-large-number-of-records-take-too-much-time-using-entity-framework?noredirect=1&lq=1)
 
## Answer

### BulkInsert

There are many solutions to insert many records in the fastest way, but the most significant and recommended solution is BulkInsert provided by [Entity Framework Extensions](http://entityframework-extensions.net/) library. By default, identity value is populated to make it even easier to use.

#### Performance Comparisons

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:--------- |:------------- |:------------- |:------------ |
|SaveChange |1,000 ms	    |2,000 ms	    |5,000 ms      |
|BulkInsert	|6 ms	        |10 ms	        |15 ms         |

#### Steps to use BulkInsert

 1. CREATE a list
 2. ADD entity to the list
 3. USE BulkInsert
 4. Done!


```csharp
using (var ctx = new CustomerContext())
{
    // 1. CREATE a list
    List<Customer> customers = new List<Customer>();
    
    foreach(var line in lines)
    {
        var customer = new Customer();
        // ...code...
        
        // 2. ADD entity to the list
        customers.Add(customer);
    }
    
    // 3. USE BulkInsert
    ctx.BulkInsert(customers);
    
    // 4. Done!
}
```

[Try it online](https://dotnetfiddle.net/Y1NxKL)

### Alternative to BulkInsert

There are some free third-party libraries alternative to Entity Framework Extensions, they are not hard to find, but we don't recommend them since they work with the simple scenario but fail at supporting complex type, inheritance, and association.

### SqlBulkCopy

Using SqlBulkCopy (for SQL Server) is without a doubt the fastest solution (very slightly faster than Entity Framework Extensions) but also the longer to implement.

Be careful; this solution adds some code complexity for the maintenance team.

#### Steps to use SqlBulkCopy

 1. CREATE a list
 2. ADD entity to the list
 3. CONVERT the list to a DataTable
 4. CREATE a SqlBulkCopy instance
 5. MAP the SqlBulkCopy to database
 6. Use WriteToServer
 7. Done!