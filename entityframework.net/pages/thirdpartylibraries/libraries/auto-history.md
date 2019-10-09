---
PermaID: 1000011
Name: Auto History
---

# AutoHistory

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