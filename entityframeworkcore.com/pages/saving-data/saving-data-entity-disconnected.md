---
permaid: 1000247
Title: EF Core Entity Disconnected - Learn How to Track Untracked Entities
MetaDescription: Unlock the power of EF Core by using Entity Disconnected to improve performance and track entities back in the change tracker. Learn what is considered a disconnected entity and how to track it back.
LastMod: 2023-02-24
tags: save change-tracker disconnected
---

# EF Core Entity Disconnected: Discover How to Track Untracked Entities

## Introduction

A DbContext instance automatically tracks entities returned from the database, and any changes made to these entities are detected when SaveChanges is called, and the database is also updated as needed.

 - Sometimes entities are queried using one context instance and then saved using a different instance. 
 - In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).

### Identify New or Existing Entities

It is necessary to determine whether to insert or update an entity. The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.

 - If the key has not been set, then the entity must be new and needs inserting. 
 - On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating. 


```csharp
using (var context = new MyContext())
{
    if (customer.CustomerId == 0)
    {
        context.Customers.Add(customer);
    }
    else
    {
        context.Customers.Update(customer);
    }

    context.SaveChanges();
}
```

You can use a built-in way to do this for any entity type and key type.


```csharp
using (var context = new MyContext())
{
    if (!context.Entry(customer).IsKeySet)
    {
        context.Customers.Add(customer);
    }
    else
    {
        context.Customers.Update(customer);
    }

    context.SaveChanges();
}
```

When the key values are not generated automatically, then you can use the `Find()` method to query for the entity.


```csharp
using (var context = new MyContext())
{
    var existingCustomer = context.Customers.Find(customer.CustomerId);
    if (existingCustomer == null)
    {
        context.Add(customer);
    }
    else
    {
        context.Entry(existingCustomer).CurrentValues.SetValues(customer);
    }

    context.SaveChanges();
}
```
