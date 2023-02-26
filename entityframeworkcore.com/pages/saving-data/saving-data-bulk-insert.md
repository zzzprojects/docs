---
permaid: 1000243
Title: EF Core Bulk Insert - Learn How to Insert Thousands of Entities
MetaDescription: Unlock the power of EF Core by using Bulk Insert to improve performance dramatically by using bulk operations. Learn what bulk operations are, why you should use it and how easily you can implement it in your project.
LastMod: 2023-02-24
tags: save insert bulk-insert
---

# EF Core Bulk Insert: Discover How to Insert Thousands of Entities

## Introduction

When you want to insert hundreds, thousands, or millions of entities using `SaveChanges()` method, you will notice that your application performance is **INSANELY** slow. Because `SaveChanges()` requires one database round-trip for every entity to insert. So if you need to insert 10000 entities, then 10000 database round-trips will be performed and your application suffers from performances issues.

Entity Framework Core has made great improvement and is way faster than EF6, but your application performance can even faster than that by using a 3rd party library [Entity Framework Extensions](https://entityframework-extensions.net/).

### BulkInsert

Entity Framework Extensions provides an extension method [BulkInsert](https://entityframework-extensions.net/bulk-insert) which requires the minimum database round-trips as compared to `SaveChanges()`. By example under the hood for SQL Server, a simple `SqlBulkCopy` could be performed.

#### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert      | 6 ms           | 10 ms          | 15 ms          |

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

Read more: [BulkInsert](https://entityframework-extensions.net/bulk-insert)
