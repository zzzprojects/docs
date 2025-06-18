---
PermaID: 1000084
Title: Unlock the power of Entity Framework by learning how to improve your insert performance efficiently.
MetaDescription: 
LastMod: 2025-06-18
Tags: save insert bulk-insert
---

# Entity Framework Insert Records: Discover How to Improve Performance

## How to do a Bulk Insert?

Inserting thousands of entities for an initial load or a file import is a typical scenario. 

**SaveChanges** method makes it quite impossible to handle this kind of situation directly from Entity Framework due to the number of database round-trips required.

SaveChanges requires one database round-trip for every entity to insert. So if you need to insert 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.


```csharp
using (var ctx = new CustomerContext())
{
    List<Customer> customers = new List<Customer>();
    
    foreach(var line in lines)
    {
        var customer = new Customer();
        // ...code...
        
        customers.Add(customer);
    }
    
    ctx.Customers.AddRange(customers);

    ctx.SaveChanges();
}
```

### StackOverflow Related Questions

 - [How to do a Bulk Insert - Linq to Entities](https://stackoverflow.com/questions/1609153/how-to-do-a-bulk-insert-linq-to-entities)
 - [How to do Bulk Insert using MySQL (like SqlBulkCopy) with Linq to Entities](https://stackoverflow.com/questions/10129001/how-to-do-bulk-insert-using-mysql-like-sqlbulkcopy-with-linq-to-entities?rq=1)

## Answer

[Entity Framework Extensions](https://entityframework-extensions.net/) library adds the BulkInsert extension method to the DbContext. **BulkInsert** requires the minimum database round-trips as compared to **SaveChanges**.


```csharp
using (var ctx = new CustomerContext())
{
    List<Customer> customers = new List<Customer>();
    
    foreach(var line in lines)
    {
        var customer = new Customer();
        // ...code...
        
        customers.Add(customer);
    }
    
    ctx.BulkInsert(customers);
}
```

[Try it online](https://dotnetfiddle.net/Y1NxKL)

[Learn more](https://entityframework-extensions.net/bulk-insert)