---
permaid: 1000239
Title: EF Core Eager and Lazy Loading - Learn How to Load Data on Demand
MetaDescription: Unlock the power of EF Core by understanding Eager and Lazy Loading, and their advantages when to use it. Learn why, when, and how to use lazy loading approach over eager loading.
LastMod: 2023-02-23
tags: query loading
---

# EF Core Eager and Lazy Loading: Discover How to Load Data on Demand

Entity Framework Core allows you to use the navigation properties in your model to load related entities. There are three common patterns used to load related data.

 - Eager loading
 - Explicit loading 
 - Lazy loading 

We have a simple model which contains two entities.


```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public virtual List<Invoice> Invoices { get; set; }
}

public class Invoice
{
    public int Id { get; set; }
    public DateTime Date { get; set; }
    public int CustomerId { get; set; }
    [ForeignKey("CustomerId")]
    public virtual Customer Customer { get; set; }
}

```

#### Eager loading 

In eager loading, the related data is loaded from the database as part of the initial query using [Include & ThenInclude](include-theninclude) methods.

 - The Include method specifies the related objects to include in the query results. 
 - It can be used to retrieve some information from the database and also want to include related entities.

Now to retrieve all customers and their related invoices we have to use the `Include` Method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .Include(c => c.Invoices)
        .ToList();
}
```

#### Explicit loading 

In explicit loading, the related data is explicitly loaded from the database at a later time. You can explicitly load a navigation property via the DbContext.Entry() method.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .Single(c => c.CustomerId == 1);

    context.Entry(customer)
        .Collection(c => c.Invoices)
        .Load();
}
```

You can get a LINQ query that represents the contents of a navigation property and then filters which related entities are loaded into memory.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .Single(c => c.CustomerId == 1);

    context.Entry(customer)
        .Collection(c => c.Invoices)
        .Query()
        .Where(i => i.Date >= new DateTime(2018, 1, 1))
        .ToList();
}
```

#### Lazy loading 

In lazy loading, the related data is transparently loaded from the database when the navigation property is accessed. To use lazy-loading, the simple way is to install the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) and enable it by calling `UseLazyLoadingProxies()` in `OnConfiguring` method on your data context. 


```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;");
}
```

EF Core will enable lazy-loading for any navigation property that is virtual and in a class that can be inherited. So in our model the `Invoice.Customer` and `Customer.Invoices` navigation properties will be lazy-loaded.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers.ToList();
}
```
