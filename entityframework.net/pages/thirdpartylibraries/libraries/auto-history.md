---
PermaID: 1000011
Title: Entity Framework AutoHistory - Learn How to Perform Auditing
MetaDescription: Unlock the power of Entity Framework by understanding how to audit your context. Learn how to audit inserted, updated, and deleted entities and save them in a log file or a database.
LastMod: 2023-02-22
Tags: third-party-library
---

# Entity Framework AutoHistory: Discover How to Perform Auditing

## Definition

**Microsoft.EntityFrameworkCore.AutoHistory** is a plugin for Microsoft.EntityFrameworkCore to support automatically recording data changes history.

AutoHistory records all the data changing history in a table named `AutoHistories`, this table will recording data UPDATE, DELETE history.

## Enable AutoHistory

To enable the automatic recording change history, override the **OnModelCreating** method in your DbContext class and call **EnableAutoHistory()** extension method with ModelBuilder object.


```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // enable auto history functionality.
        modelBuilder.EnableAutoHistory();
    }
}
```

## Ensure AutoHistory

To ensure the automatic history, call **EnsureAutoHistory()** extension method with DbContext object.


```csharp
using (var context = new BloggingContext())
{
    context.EnsureAutoHistory();
    // code here
}
```

## Requirements

### Entity Framework Version

 - Entity Framework Core

[Learn more](https://github.com/Arch/AutoHistory)