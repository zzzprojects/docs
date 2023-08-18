---
title: The Fluent API ToView Method
description: Usage of the Fluent API ToView Method in Entity Framework Core
canonical: /configuration/fluent-api/toview-method
status: Published
lastmod: 2023-02-27
---

# EF Core ToView

The `ToView` method is applied to a type to specify the name of the database view or table that the type should map to. The `ToView` method is mainly used for mapping [query types](/query-types) to tables or views.


The following example specifies that the `OrderHeader` query type should map to a database view named `vw_OrderHeaders`:

```csharp
public class SampleContext : DbContext
{
    public DbQuery<OrderHeader> OrderHeaders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Query<OrderHeader>()
            .ToView("vw_OrderHeaders");
    }
}

public class OrderHeader
{
    public string CustomerName { get; set; }
    public DateTime DateCreated { get; set; }
    public int TotalItems { get; set; }
    public decimal TotalPrice { get; set; }
}
```

An overload of the method maps the entity to a non-default schema:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Query<OrderHeader>()
            .ToView("vw_OrderHeaders", "Accounts");
}
```

### Data Annotations
There is no Data Annotation equivalent to the `ToView` method.