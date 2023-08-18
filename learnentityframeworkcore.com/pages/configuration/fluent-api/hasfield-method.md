---
title: The Fluent API HasField method
description:
canonical: configuration/fluent-api/hasfield-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasField

The Entity Framework Core Fluent API `HasField` method configures a backing field as part of the model. The backing field will be read from and written to, instead of the property. You will use the `HasField` method when the [name of the backing field doesn't match one of the conventions](/conventions#backing-fields).

In the following example, the `RecommendedRetailPrice` property has a backing field named `rrp`, which will not be discovered by convention:

```csharp
public class Book
{
    private decimal rrp;
    public int BookId { get; set; }
    public string Title { get; set; }
    public int PublisherId { get; set; }
    public Publisher Publisher { get; set; }
    public decimal RecommendedRetailPrice {
        get { return rrp; }
        set { rrp = value; }
    }
}
```
The next example illustrates the use of `HasField` to configure the backing field as part of the model:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>().Property(p => p.RecommendedRetailPrice).HasField("rrp");
}
```

### Data Annotations 

There is no Data Annotation equivalent to the `HasField` method.