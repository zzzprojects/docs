---
title: The Fluent API HasQueryFilter Method
description:
canonical: /configuration/fluent-api/hasqueryfilter-method
status: Published
lastmod: 2025-07-11
---

# EF Core HasQueryFilter

The Entity Framework Core Fluent API `HasQueryFilter` method is used to apply a global query filter to a specific entity, so that the filter is included in all query statements generated for the entity by EF Core. The most common use cases for this feature include properties that indicate that an entity instance is deleted, or not otherwise active, and a tenant identifier in an application that houses data for multiple tenants.

The following example illustrates the second scenario. The code presents two entities; a `Magazine` and a `Story`. They might represent part of a model for a web application that caters for multiple magazines in the same database. In this example, the Magazine is the tenant.

```csharp
public class Magazine
{
    public int MagazineId { get; set; }
    public string Title { get; set; }
}

public class Story
{
    public int StoryId { get; set; }
    public string Headline { get; set; }
    public string Content { get; set; }
    public int MagazineId { get; set; }
    public Magazine Magazine { get; set; }
}
```
The `HasQueryFilter` method is applied to the Magazine entity in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Magazine>().HasQueryFilter(m => EF.Property<int>(m, "MagazineId") == 1);
}
```
In this case the value that is used to filter queries by is hard coded. Normally, it will be based on the result of whatever algorithm is used to determine the current `MagazineId` (a global variable, a custom header value, the URL etc). Any query that includes the `Magazine` entity will have the filter applied e.g.

```csharp
var stories = db.Stories.Include(s => s.Magazine);
```
If you do not want to have the filter applied, use the `IgnoreQueryFilters` method:
```csharp
var stories = db.Stories.Include(s => s.Magazine).IgnoreQueryFilters();
```

The next example shows how to use the query filter to automatically exclude products that have been flagged as discontinued from all queries. The model definition:

```csharp
public class Product
{
    public int ProductId { get; set; }
    public string ProductName { get; set; }
    public bool Discontinued { get; set; }
}
```
Again, the query filter is applied in the `OnModelCreating` method, and will result in queries that reference the `Product` entity only returning those that have not been discontinued:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>().HasQueryFilter(p => EF.Property<bool>(p, "Discontinued") == false);
}
```
The query is defined:

```csharp
var products = context.Products.ToList();
```
The resulting SQL that is generated automatically includes the filter:

```sql {data-lines="5"}
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT "p"."ProductId", "p"."Discontinued", "p"."ProductName"
      FROM "Products" AS "p"
      WHERE "p"."Discontinued" = 0
Total Products: 8
```

### Data Annotations

There is no Data Annotations equivalent for the `HasQueryFilter` method.