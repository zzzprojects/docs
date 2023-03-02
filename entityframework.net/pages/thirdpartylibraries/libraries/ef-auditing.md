---
PermaID: 1000164
Title: Entity Framework EFAuditing - Learn How to Perform Auditing
MetaDescription: Unlock the power of Entity Framework by understanding how to audit your context. Learn how to audit inserted, updated, and deleted entities and save them in a log file or a database.
LastMod: 2023-02-22
Tags: third-party-library
---

# Entity Framework EFAuditing: Discover How to Perform Auditing

## Definition

**EFAuditing** is a library that implements Auditing for Entity Framework Core based DbContexts. It is extensible to allow other logging providers like MongoDB, Azure tables etc.

Derive you context from AuditDbContext.


```csharp
public partial class MyContext : AuditingDbContext
{
    public MyContext() : base("MyContextDB")
    {
        this.Configuration.LazyLoadingEnabled = false;

    }
    public DbSet<Customer> Customers { get; set; }
    public DbSet<Invoice> Invoices { get; set; }
    public DbSet<InvoiceItem> InvoiceItems { get; set; }
}
```

Use the overloaded SaveChange(string userName) instead of the standard SaveChanges().


```csharp
using (var context = new EnumTestContext())
{
    // code here
    context.SaveChanges("UserName");
}
```

## Requirements

### Entity Framework Version

 - Entity Framework 6 or later.

[Learn more](https://github.com/johannbrink/EFAuditing)