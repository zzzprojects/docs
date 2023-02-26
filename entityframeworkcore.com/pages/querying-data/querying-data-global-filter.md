---
permaid: 1000234
Title: EF Core Global Filter - Learn How to Filter your Query Globally
MetaDescription: Unlock the power of EF Core by using Global Filter to filter all your queries globally. Learn how a global filter can help to simplify your LINQ query and support scenarios such as a tenant id.
LastMod: 2023-02-23
tags: query filter
---

# EF Core Global Filter: Discover How to Filter your Query Globally

## Introduction

Entity Framework Core 2.0 introduces global query filters that can be applied to entities when a model is created. It makes it easier to build multi-tenant applications and support soft deleting of entities.

 - It allows us to specify a filter in the model level that is automatically applied to all queries that are executed in the context of the specified type. 
 - It means that entity framework automatically adds the filter in the where clause before executing the LINQ queries. 
 - Usually, Global query filters are applied in `OnModelCreating` method of context. 

Here is a simple model which contains only one entity i.e., Customer


```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string Name { get; set; }
    public bool IsDeleted { get; set; }
}
```

Global query filters are defined when database context builds the model. So we need to configure the global query filter in `OnModelCreating` method of context class, using `HasQueryFilter` method, and we can apply the global filter on the entity type.


```csharp
public class MyContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Customer>().HasQueryFilter(c => !c.IsDeleted);
    }
}
```

The expression passed in HasQueryFilter method is automatically applied to any LINQ queries for Customer Type. We have four records in the Customers table in the database.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/global-filters.png" alt="Global filters">

One record is soft deleted already, and the `IsDeleted` column contains a **True** value for that record. Now if we retrieve all the all the customers from the database using the LINQ query.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers.ToList();
}
```

We will get only three records, but in the database, we have four records, Because the global filter has filtered records and returns only those records whose `IsDeleted` column contains **False** value.

In some cases, we do not need these filters to be applied, we can also disable filter for individual LINQ queries using the `IgnoreQueryFilters()` method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .IgnoreQueryFilters().ToList();
}
```

Now the filter won't be applied, and you will get all the records.
