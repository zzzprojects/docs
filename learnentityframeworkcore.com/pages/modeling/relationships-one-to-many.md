---
title: Entity Framework Core One-To-Many Relationships Conventions
description: How to configure one-to-many relationships in EF Core using conventions, Fluent API, shadow foreign keys, and required or optional relationships.
canonical: /modeling/relationships-one-to-many
status: Published
lastmod: 2026-11-23
---

# One-to-Many Relationships in EF Core

A one-to-many relationship associates one principal entity with multiple dependent entities. For example, an order can contain many order items, while each order item belongs to at most one order. EF Core can discover this relationship by convention, or you can configure it explicitly using Fluent API.

## Basic One-to-Many Relationship

An `Order` can contain many `OrderItem` records. The following example uses only the collection navigation on `Order`:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public ICollection<OrderItem> Items { get; set; } = new List<OrderItem>();
}

public class OrderItem
{
    public int OrderItemId { get; set; }
    public string ProductName { get; set; } = null!;
}
```

EF Core discovers a one-to-many relationship from `Order.Items`. Since `OrderItem` does not declare a foreign key property, EF Core creates a [shadow foreign key](/model/shadow-properties) named `OrderId`. The property exists in the EF Core model and as a column in the database, but not in the C# class.

With this model, the shadow foreign key is nullable by convention, so an `OrderItem` can exist without an associated `Order`. In a typical order-processing application, order items are usually required to belong to an order; we will model that more common rule in the explicit foreign key example below.

See the [official EF Core one-to-many documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many) for additional relationship patterns.

## Configure One-to-Many with Fluent API

EF Core can discover the basic example without `OnModelCreating`. You can also express the mapping explicitly using the **Has/With** pattern, where `HasMany()` identifies the collection navigation and `WithOne()` identifies the other end of the relationship:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .HasMany(o => o.Items)
        .WithOne()
        .HasForeignKey("OrderId")
        .IsRequired(false);
}
```

This configuration uses the same `Order` and `OrderItem` classes as the basic example. `WithOne()` has no argument because `OrderItem` has no inverse navigation property; `HasForeignKey("OrderId")` specifies the shadow foreign key, and `IsRequired(false)` makes the relationship optional.

When both entities have navigation properties, you can instead use `HasMany(o => o.Items).WithOne(i => i.Order)`. The equivalent configuration can start from `OrderItem` with `HasOne(i => i.Order).WithMany(o => o.Items)`; configure the relationship once, not from both ends separately.

For required relationships, use `IsRequired()` or a non-nullable foreign key as in the example below. Use `OnDelete()` when you need to customize deletion behavior; see [Cascade Delete in EF Core](/saving/cascade-delete) for the available behaviors and their implications.

## Inverse Navigation Property

You can add an inverse reference navigation property to the dependent entity, allowing navigation in both directions:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public ICollection<OrderItem> Items { get; set; } = new List<OrderItem>();
}

public class OrderItem
{
    public int OrderItemId { get; set; }
    public string ProductName { get; set; } = null!;
    public Order? Order { get; set; }
}
```

`OrderItem.Order` provides access to the principal order; `Order.Items` provides access to its items. No foreign key is declared in `OrderItem`, so EF Core still creates a shadow `OrderId` foreign key. Because `OrderItem.Order` is nullable, this example is optional with nullable reference types enabled.

## Optional One-to-Many Relationship

Instead of a shadow foreign key, declare a nullable foreign key explicitly if an item may temporarily exist without an order—for example, while assembling a draft order:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public ICollection<OrderItem> Items { get; set; } = new List<OrderItem>();
}

public class OrderItem
{
    public int OrderItemId { get; set; }
    public string ProductName { get; set; } = null!;
    public int? OrderId { get; set; }
    public Order? Order { get; set; }
}
```

EF Core discovers `OrderItem.OrderId` as the foreign key. Because it is a nullable `int?`, the relationship is optional. The nullable `Order?` navigation is consistent with this configuration when nullable reference types are enabled.

Optional relationships use `ClientSetNull` by convention: EF Core sets the foreign key to null for tracked dependents when the relationship is severed or the principal is deleted. With untracked dependents, the database may reject a principal deletion unless a database-side action has been configured. For more details, see [Cascade Delete in EF Core](/saving/cascade-delete).

## One-to-Many with Explicit Foreign Key

In a typical order-processing model, every saved order item belongs to an order. Define a non-nullable `OrderId` foreign key to make the relationship required:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public ICollection<OrderItem> Items { get; set; } = new List<OrderItem>();
}

public class OrderItem
{
    public int OrderItemId { get; set; }
    public string ProductName { get; set; } = null!;
    public int OrderId { get; set; }
    public Order Order { get; set; } = null!;
}
```

EF Core discovers the one-to-many relationship by convention. Because `OrderItem.OrderId` is a non-nullable `int`, each order item must reference an order, although an order can exist without any items. Required relationships use cascade delete by convention; to change that behavior, see [Cascade Delete in EF Core](/saving/cascade-delete).

### Without an Inverse Navigation Property

The dependent entity does not need an `Order` navigation property when the foreign key is explicit:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public ICollection<OrderItem> Items { get; set; } = new List<OrderItem>();
}

public class OrderItem
{
    public int OrderItemId { get; set; }
    public string ProductName { get; set; } = null!;
    public int OrderId { get; set; }
}
```

EF Core discovers the required relationship from `Order.Items` and `OrderItem.OrderId`; an inverse reference navigation is not required. See the [official documentation on one-to-many relationships without navigation to the principal](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-without-navigation-to-principal).

## Other One-to-Many Configurations

EF Core supports additional one-to-many configurations. The following references cover less common scenarios without expanding the introductory examples.

| Configuration | When to use it | Documentation |
|---|---|---|
| Without navigation to dependents | The principal does not expose a collection navigation. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-without-navigation-to-dependents) |
| Without navigations | Neither entity exposes navigation properties; explicit configuration is necessary. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-with-no-navigations) |
| Alternate key | The dependent references an alternate key instead of the principal's primary key. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-with-alternate-key) |
| Composite foreign key | The dependent uses more than one foreign key property. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-with-composite-foreign-key) |

When conventions do not produce the intended relationship, Fluent API methods such as `HasMany()`, `WithOne()`, and `HasForeignKey()` allow you to configure it explicitly.

## External Resources

The following videos demonstrate different ways to define and configure one-to-many relationships in EF Core. Together, they cover explicit foreign keys, shadow foreign keys, required and optional relationships, and navigation properties.

### Video 1 - Master One-to-Many EF Core Relationships in 10 Minutes | ASP.NET Core Minimal API Tutorial — Poorna Soysa

<iframe width="560" height="315" src="https://www.youtube.com/embed/1P_O7j6J-m0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Poorna Soysa demonstrates a one-to-many relationship between `Customer` and `Order`, using an explicit foreign key, navigation properties, and Fluent API. The example also shows how the relationship is represented in SQL Server after applying a migration.

**Key timestamps:**

- [0:56 — Introducing the one-to-many relationship between Customer and Order](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=56s)
- [1:53 — Defining the CustomerId foreign key and navigation properties](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=113s)
- [3:11 — Configuring the relationship with Fluent API](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=191s)
- [4:36 — Generating a migration and inspecting the database](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=276s)

Although EF Core can discover this relationship by convention, Fluent API provides additional control when needed.

### Video 2 - Shadow Properties - EF Core - Foreign Keys — Full Stack Amigo

<iframe width="560" height="315" src="https://www.youtube.com/embed/SSqB_Z48co4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Full Stack Amigo demonstrates how EF Core creates and manages a shadow foreign key in a one-to-many relationship between `User` and `Tweet`. The example shows that a foreign key can exist in the EF Core model and database without being declared as a property in the dependent C# class.

**Key timestamps:**

- [0:00 — Removing the explicit foreign key and dependent navigation](https://www.youtube.com/watch?v=SSqB_Z48co4&t=0s)
- [1:30 — Inspecting the UserId shadow foreign key in SQL Server](https://www.youtube.com/watch?v=SSqB_Z48co4&t=90s)
- [2:30 — Accessing and querying shadow property values](https://www.youtube.com/watch?v=SSqB_Z48co4&t=150s)
- [4:48 — Configuring a required shadow foreign key with IsRequired()](https://www.youtube.com/watch?v=SSqB_Z48co4&t=288s)

Shadow foreign keys can be optional or required. Their nullability depends on the relationship conventions and configuration, not simply on whether the foreign key is declared in the C# class.

### Video 3 - EF Core 6: How to build and manage relationships (Part 4) — Building SaaS with Gabriel

<iframe width="560" height="315" src="https://www.youtube.com/embed/ujf8lGv8OTo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Building SaaS with Gabriel demonstrates a one-to-many relationship between `Grade` and `Student`. The relevant sections show how EF Core initially generates a nullable shadow foreign key and how declaring a non-nullable `GradeId` property changes the relationship to required. The example also introduces an inverse navigation property.

**Key timestamps:**

- [4:31 — Introducing the Grade–Student one-to-many relationship](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=271s)
- [6:28 — Examining the migration with a nullable shadow foreign key](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=388s)
- [7:31 — Declaring a non-nullable GradeId to require a principal](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=451s)
- [8:47 — Adding the Student.Grade inverse navigation property](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=527s)

The most useful comparison is between the migrations before and after adding `int GradeId`. The one-to-many demonstration runs from approximately 4:31 to 11:03; later sections cover other relationship types.

## Summary

EF Core supports one-to-many relationships through conventions or explicit configuration. The appropriate configuration depends on whether the relationship is required or optional and which foreign key and navigation properties your entities expose.

- **Shadow foreign keys:** EF Core can create a foreign key when no corresponding property is declared in the dependent entity.
- **Optional and required relationships:** A nullable foreign key makes the relationship optional, while a non-nullable foreign key makes it required.
- **Navigation properties:** Relationships can have navigations in both directions, one direction, or neither.
- **Explicit configuration:** Fluent API provides control when conventions do not match the intended relationship.

## Related Articles

- [One-to-One Relationships](/modeling/relationships-one-to-one) — Configure relationships with one dependent per principal.
- [Many-to-Many Relationships](/modeling/relationships-many-to-many) — Configure relationships where entities can be associated with multiple entities on both sides.

## FAQ

### Can a shadow foreign key be required?

Yes. A shadow foreign key can be required even when no foreign key property is declared in the dependent entity. Use `IsRequired()` in Fluent API to configure a required relationship when EF Core would otherwise treat it as optional.

### Does a required one-to-many relationship mean the principal must have dependents?

No. A required relationship means each dependent must be associated with a principal. It does not require the principal to have any dependents. For example, an order can exist without items, even when every order item must belong to an order.

### Are navigation properties required for a one-to-many relationship?

No. A one-to-many relationship can have navigations on both sides, on only one side, or on neither side. EF Core can discover many relationships by convention when suitable navigations are present. Without navigation properties, the relationship must be configured explicitly.

### Does a non-nullable foreign key automatically load the related entity?

No. A non-nullable foreign key makes the relationship required, but it does not automatically load the related entity into its navigation property. You can use `Include()` to load related data as part of a query. See [Eager Loading in EF Core](/querying/eager-loading) for more details.