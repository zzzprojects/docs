---
title: Table in EF Core
description: Learn how to configure table names, schemas, views, comments, constraints, triggers, and SQL Server table features in EF Core.
canonical: /table
status: Published
lastmod: 2026-11-23
---

# Table in EF Core

By convention, Entity Framework Core maps an entity to a table named after its `DbSet` property, or its class name if no `DbSet` exists. You can customize this mapping using Data Annotations or Fluent API. For more details, see the [official EF Core documentation](https://learn.microsoft.com/en-us/ef/core/modeling/entity-types?tabs=data-annotations#table-name).

For example, to map the `Blog` entity to a table named `blogs`:

**Data Annotations**

```csharp
[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; } = string.Empty;
}
```

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable("blogs");
```

Both approaches map `Blog` to the `blogs` table, overriding its conventional table name.

## Configure Table Mapping

### Table Name and Schema

By default, EF Core uses the database's default schema. You can specify a different schema along with the table name.

**Data Annotations**

```csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; } = string.Empty;
}
```

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable("blogs", schema: "blogging");
```

The entity is mapped to the `blogging.blogs` table. Schema support depends on the database provider; for example, SQL Server supports schemas, while SQLite does not.

For more details about these configurations, see the [Table Attribute](/configuration/data-annotation-attributes/table-attribute) and [ToTable Method](/configuration/fluent-api/totable-method) guides.

### View Mapping

You can map an entity to a database view using Fluent API.

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToView("blogsView", schema: "blogging");
```

EF Core queries `Blog` entities through the `blogging.blogsView` view instead of the default table mapping. The view must already exist, as EF Core does not create it through migrations.

### Table Comment

You can add a descriptive comment to a database table using Data Annotations or Fluent API.

**Data Annotations**

```csharp
[Comment("Stores blog information")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; } = string.Empty;
}
```

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable(tb => tb.HasComment("Stores blog information"));
```

Both configurations add the comment to the mapped table when supported by the database provider.

### Exclude from Migrations

You can exclude a table from EF Core migrations while keeping its entity in the model.

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable("blogs", tb => tb.ExcludeFromMigrations());
```

EF Core can still query and update the `blogs` table, but migrations exclude it from schema management while this configuration is applied. The table must be managed separately.

## Configure Additional Table Features

### Table Trigger

You can specify that a database table has a trigger using Fluent API.

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable(tb => tb.HasTrigger("TRG_InsertUpdateBlog"));
```

This registers the trigger in the EF Core model but does not create it in the database. Creating the trigger through migrations requires additional SQL, such as `migrationBuilder.Sql()`.

### Check Constraint

You can configure a check constraint to restrict the values allowed in a database table.

For example, consider a `Product` entity with a `Price` property:

```csharp
public class Product
{
    public int ProductId { get; set; }
    public decimal Price { get; set; }
}
```

**Fluent API**

```csharp
modelBuilder.Entity<Product>()
    .ToTable(tb => tb.HasCheckConstraint(
        "CK_Product_Price", "[Price] > 0"));
```

This configures a check constraint that prevents negative or zero prices when enforced by the database. The SQL expression uses SQL Server syntax and may need adjustment for other providers.

### SQL Server-Specific Features

The following configurations are specific to SQL Server and use Fluent API.

#### SQL OUTPUT Clause

SQL Server uses the `OUTPUT` clause to optimize saving changes. However, this clause is incompatible with certain table features, including some triggers.

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable(tb => tb.UseSqlOutputClause(false));
```

This disables the `OUTPUT` clause for the mapped table, allowing EF Core to use an alternative, generally less efficient saving strategy.

#### Memory-Optimized Table

You can configure a SQL Server table as memory-optimized for workloads that benefit from in-memory data storage.

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable(tb => tb.IsMemoryOptimized());
```

EF Core configures the table as memory-optimized when generating its database schema. This requires the appropriate SQL Server support and database configuration.

#### Temporal Table

SQL Server temporal tables automatically maintain a history of changes to their data.

**Fluent API**

```csharp
modelBuilder.Entity<Blog>()
    .ToTable(tb => tb.IsTemporal());
```

When EF Core creates the table through migrations, SQL Server also creates a history table that preserves previous versions of updated or deleted rows. For more details, see the [official documentation on temporal tables](https://learn.microsoft.com/en-us/ef/core/providers/sql-server/temporal-tables).

## External Resources - Table Mapping

The following videos complement the table mapping examples in this article with practical demonstrations of Data Annotations, database views, and SQL Server temporal tables. They show how EF Core maps entities to database objects and how these configurations affect the resulting database structure.

### Video 1 - Entity Framework Core Part 8 - Attribute-Based Configuration

<iframe width="560" height="315" src="https://www.youtube.com/embed/uQfeppE2SLE" title="Entity Framework Core Part 8 - Attribute-Based Configuration" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In this tutorial, Jasper Kent explains how EF Core determines table names from `DbSet` properties and entity class names. He then demonstrates how the `[Table]` attribute overrides these conventions and verifies the resulting table names in SQL Server.

**Key timestamps:**

- **[02:25](https://www.youtube.com/watch?v=uQfeppE2SLE&t=145s)** — Explains how EF Core uses `DbSet` property names to determine table names.
- **[04:58](https://www.youtube.com/watch?v=uQfeppE2SLE&t=298s)** — Shows how EF Core uses the entity class name when no `DbSet` is defined.
- **[05:25](https://www.youtube.com/watch?v=uQfeppE2SLE&t=325s)** — Demonstrates using `[Table]` to customize database table names.
- **[06:21](https://www.youtube.com/watch?v=uQfeppE2SLE&t=381s)** — Verifies the generated table names in SQL Server.

The video provides a useful introduction to table naming conventions before moving into other Data Annotations. Although it uses an older version of EF Core, the demonstrated `[Table]` configuration remains applicable.

### Video 2 - Using a View in Entity Framework Core - Entity Framework Core Tutorial 

<iframe width="560" height="315" src="https://www.youtube.com/embed/p1gA1iX081A" title="Uso de una vista en Entity Framework Core - Tutorial de Entity Framework Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In this tutorial, Coding Under Pressure demonstrates how to map an EF Core entity to an existing SQLite database view. The example defines a class for the view's results, configures the mapping using `HasNoKey()` and `ToView()`, and verifies that EF Core can query the view.

**Key timestamps:**

- **[02:29](https://www.youtube.com/watch?v=p1gA1iX081A&t=149s)** — Defines the class representing the data returned by the view.
- **[03:49](https://www.youtube.com/watch?v=p1gA1iX081A&t=229s)** — Explains why the example requires keyless entity configuration.
- **[04:44](https://www.youtube.com/watch?v=p1gA1iX081A&t=284s)** — Configures the entity using `HasNoKey()` and `ToView()`.
- **[06:45](https://www.youtube.com/watch?v=p1gA1iX081A&t=405s)** — Verifies the data retrieved from the SQLite view.

Unlike the `Blog` example in this article, the video's entity has no primary key, so it also requires `HasNoKey()`. A primary key is not required for every database view mapping, and `ToView()` can also be used with entities that have keys.

### Video 3 - Temporal Tables - (System-Version Tables) - EF Core - SQL Server

<iframe width="560" height="315" src="https://www.youtube.com/embed/asF1ZRuLB0Y" title="Temporal Tables - (System-Version Tables) - EF Core - SQL Server" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This video from Full Stack Amigo demonstrates how to configure SQL Server temporal tables using `ToTable()` and `IsTemporal()`. It shows how SQL Server maintains a history table, how to customize the history table and period columns, and how EF Core can query previous versions of records.

**Key timestamps:**

- **[01:43](https://www.youtube.com/watch?v=asF1ZRuLB0Y&t=103s)** — Configures a temporal table using `ToTable()` and `IsTemporal()`.
- **[02:56](https://www.youtube.com/watch?v=asF1ZRuLB0Y&t=176s)** — Inspects the temporal and history tables in SQL Server.
- **[04:18](https://www.youtube.com/watch?v=asF1ZRuLB0Y&t=258s)** — Customizes the history table name and period columns.
- **[05:36](https://www.youtube.com/watch?v=asF1ZRuLB0Y&t=336s)** — Demonstrates querying historical data with EF Core.

The video extends the `IsTemporal()` configuration shown in this article by demonstrating the resulting database structure and historical queries. It uses EF Core 6, but the basic temporal table configuration remains relevant to EF Core 10.

## Related Articles

Explore more ways to configure entity mapping in EF Core:

- [Table Advanced](/mapping/table-advanced)
- [Column](/mapping/column)

## FAQ

### Can I use Data Annotations and Fluent API together to configure a table?

Yes. You can combine both approaches, but Fluent API takes precedence when they configure the same setting. For example, if `[Table]` specifies one table name and `ToTable()` specifies another, EF Core uses the name configured through Fluent API.

### Does mapping an entity to a database view require HasNoKey()?

No. You can use `ToView()` with an entity that has a primary key. Use `HasNoKey()` when the entity does not have a key, such as a read-only result type mapped to a database view.

### Can I use all these table configurations with SQLite?

No. Some configurations depend on the database provider. For example, SQLite does not support schemas, while `UseSqlOutputClause()`, `IsMemoryOptimized()`, and `IsTemporal()` are specific to SQL Server.