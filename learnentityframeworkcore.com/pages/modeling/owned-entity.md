---
title: EF Core Owned Entity Types
description: Learn how to configure Owned Entity Types in EF Core with OwnsOne and OwnsMany, including same-table, separate-table, nested, and JSON mappings.
canonical: /modeling/owned-entity
status: Published
lastmod: 2026-11-18
---

# Owned Entity Types in EF Core

An owned entity represents data that belongs to another entity and is treated as part of that owner. For example, an `Order` can own `OrderDetails`: the details belong to that specific order rather than being modeled as an independent entity on their own.

This can look similar to a [one-to-one relationship](/modeling/relationships-one-to-one) or a [one-to-many relationship](/modeling/relationships-one-to-many), depending on whether the owner has one owned object or a collection. **Use a regular relationship when you want to connect entities without configuring ownership. Use an owned entity when the object is conceptually part of its owner and should be modeled through an ownership relationship.** Owned navigations are also loaded with the owner without requiring `Include`.

Use `OwnsOne` for a single owned object and `OwnsMany` for a collection. The examples below start with the most common case: one owned reference stored in the same table as its owner.

## Own One Entity — Same Table

Use `OwnsOne` when an entity has a single owned object.

For example, an `Order` can own an `OrderDetails` object:

```csharp
public class Order
{
    public int Id { get; set; }

    public OrderDetails Details { get; set; } = new();
}

public class OrderDetails
{
    public string Status { get; set; } = "";
    public string ReferenceNumber { get; set; } = "";
}
```

Configure the `Details` navigation as an owned entity in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .OwnsOne(o => o.Details);
}
```

EF Core now treats `OrderDetails` as part of the `Order` ownership model. `Order` is the owner, and `OrderDetails` is the owned entity.

`OrderDetails` does not need an `Id` property in the C# class. Because an owned type is still an entity type in EF Core, it needs a key, but EF Core normally creates that key as a shadow property for you. With `OwnsOne`, the shadow key uses the same value as the owner's primary key.

Conceptually, the model looks like this:

```text
Order
└── OrderDetails
```

With a relational provider, a reference owned entity is mapped to the same table as its owner by default. EF Core uses table splitting, so the properties of both `Order` and `OrderDetails` are stored in the `Orders` table.

For example, the table can contain columns similar to these:

| Column | Source |
|---|---|
| `Id` | `Order.Id` |
| `Details_Status` | `OrderDetails.Status` |
| `Details_ReferenceNumber` | `OrderDetails.ReferenceNumber` |

The important distinction is that **ownership defines how `OrderDetails` belongs to `Order` in the EF Core model, while sharing the same table is only the default relational mapping**.

The owned entity can instead be stored in a separate table without changing that ownership.

## Own One Entity — Separate Table

An owned reference does not have to share the same table as its owner.

Using the same `Order` and `OrderDetails` model from the previous example, you can map the owned entity to a separate table with `ToTable`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .OwnsOne(
            o => o.Details,
            owned =>
            {
                owned.ToTable("OrderDetails");
            });
}
```

The EF Core model does not change: `Order` is still the owner, and `OrderDetails` is still an owned entity type.

What changes is only the relational storage:

| Type | Table |
|---|---|
| `Order` | `Orders` |
| `OrderDetails` | `OrderDetails` |

The `OrderDetails` table uses the owner's key as the key for the owned reference. Mapping it to another table does not turn it into an independent entity.

> Ownership describes how the types relate in the EF Core model. `ToTable` changes only where the owned data is stored.

You can also use the `[Table]` attribute to map an owned CLR type to a separate table. Fluent API is generally more flexible when table mapping depends on a specific owned navigation or when the same CLR type is used in multiple places.

Use a separate table when the data still belongs conceptually to the owner but you do not want its columns stored in the owner's table.

So far, each `Order` has owned a single `OrderDetails` instance. When the owner needs multiple owned instances, the model changes from a single owned object to an owned collection.

## Own Many Entities

Use `OwnsMany` when an entity owns a collection.

For example, an `Order` can own multiple `OrderLine` objects:

```csharp
public class Order
{
    public int Id { get; set; }

    public List<OrderLine> Lines { get; set; } = [];
}

public class OrderLine
{
    public string ProductName { get; set; } = "";
    public int Quantity { get; set; }
}
```

Configure the collection with `OwnsMany`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .OwnsMany(
            o => o.Lines,
            owned =>
            {
                owned.ToTable("OrderLines");
            });
}
```

EF Core now treats each `OrderLine` as an owned entity that belongs to a specific `Order`.

Conceptually, the model is:

```text
Order
└── OrderLine[]
```

Because an `Order` can have several lines, the owner's key alone cannot distinguish one `OrderLine` from another.

You normally do not need to add an `Id` property to `OrderLine` just for EF Core. By convention, EF Core creates the additional key value it needs as a shadow property and combines it with the ownership foreign key.

Conceptually, the default key has a shape similar to:

```text
OrderId + Id
```

This allows EF Core to distinguish each line within its owner without requiring that key to appear in the C# class.

The practical difference between `OwnsOne` and `OwnsMany` is therefore:

| Configuration | Instances per owner | Default key behavior |
|---|---:|---|
| `OwnsOne` | One | EF Core can use a shadow key based on the owner's key |
| `OwnsMany` | Many | EF Core also needs a value to distinguish each item within the owner |

You normally only need to configure the key yourself when the default key design does not fit your model.

Ownership can vary not only by cardinality. It can also be nested, allowing an owned entity type to own another entity type.

## Nested Owned Entities

An owned entity can itself own another entity.

For example, `Order` can own `OrderDetails`, and `OrderDetails` can own a `ShippingAddress`:

```csharp
public class Order
{
    public int Id { get; set; }

    public OrderDetails Details { get; set; } = new();
}

public class OrderDetails
{
    public string ReferenceNumber { get; set; } = "";

    public Address ShippingAddress { get; set; } = new();
}

public class Address
{
    public string Street { get; set; } = "";
    public string City { get; set; } = "";
}
```

Configure the nested ownership inside the configuration for `OrderDetails`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .OwnsOne(
            o => o.Details,
            details =>
            {
                details.OwnsOne(d => d.ShippingAddress);
            });
}
```

EF Core now builds two ownership relationships:

```text
Order
└── OrderDetails
    └── Address
```

`OrderDetails` is owned by `Order`, while `Address` is owned by `OrderDetails`.

Each owned navigation defines a distinct owned entity type in the EF Core model. This matters when the same CLR type is reused through multiple owned navigations, because each navigation can have its own configuration.

With a relational provider, these reference owned types are mapped to the owner's table by default when no different table mapping is configured.

In this example, the default storage can therefore remain in the `Orders` table:

| Model element | Default table |
|---|---|
| `Order` | `Orders` |
| `OrderDetails` | `Orders` |
| `Address` | `Orders` |

The important point is that nesting changes the ownership structure of the model, not necessarily the number of tables used to store it.

A nested object such as `Address` may also be a candidate for a Complex Type when it represents a pure value with no entity identity. The choice between these two modeling options is covered later in this article.

## Configure Owned Entity Types

The previous examples use Fluent API because ownership is configured for a specific navigation and additional settings can be applied in the same place.

For most EF Core providers, a regular CLR type is not treated as owned merely because it appears on another entity. Ownership is therefore normally designated explicitly.

### Convention

For most providers, ownership is not discovered automatically from the CLR model.

You therefore normally designate ownership with:

- `OwnsOne`
- `OwnsMany`
- `[Owned]`

Azure Cosmos DB is an important exception because its conventions treat related entity types as owned by default.

For relational applications, it is safer to think of ownership as an explicit modeling decision rather than something inferred only from the shape of the CLR classes.

### Use the `[Owned]` Attribute

You can mark a CLR type as owned with the `[Owned]` attribute:

```csharp
using Microsoft.EntityFrameworkCore;

[Owned]
public class OrderDetails
{
    public string Status { get; set; } = "";
    public string ReferenceNumber { get; set; } = "";
}
```

Applying `[Owned]` marks the CLR type as an owned type wherever EF Core uses it as part of the model.

This is useful when a CLR type is intended to be used consistently as an owned type.

The difference in scope is important: `[Owned]` marks the CLR type, while `OwnsOne` and `OwnsMany` configure ownership for a particular navigation.

### Use Fluent API

Use Fluent API when ownership needs to be expressed for a specific navigation or when you need additional configuration such as:

```csharp
ToTable(...)
HasForeignKey(...)
HasKey(...)
Property(...)
```

Fluent API is also the appropriate choice for owned collections and nested ownership because those scenarios require navigation-specific configuration.

### Which Configuration Should You Use?

| Need | Recommended configuration |
|---|---|
| Designate a specific owned reference | `OwnsOne` |
| Designate an owned collection | `OwnsMany` |
| Designate a CLR type as owned wherever it is referenced | `[Owned]` |
| Configure table, key, foreign key, or nested ownership | Fluent API |

For the examples in this article, Fluent API is preferred because it keeps the ownership structure and its mapping decisions visible in one place.

## Owned Entity or Complex Type?

Owned entity types and [Complex Types](/modeling/complex-type) can look similar in a C# model, but they represent different concepts in EF Core.

A useful way to start the decision is to ask: **does this object need to behave as an entity inside the EF Core model, or is it simply a value that belongs to something else?**

An owned type is still an **entity type**. EF Core gives it a key and manages it through an ownership relationship, even when that key exists only as a shadow property and never appears in the C# class.

A complex type represents a **value** within another model element. It has no identity or key of its own and is not tracked as a separate entity.

In EF Core 10, this distinction is especially important because Complex Types support more scenarios that previously required owned entity types, including collections and JSON mapping.

| Question | Owned Entity | Complex Type |
|---|---|---|
| Has identity in the EF Core model? | Yes | No |
| Has a key? | Yes, often created as a shadow key | No |
| Tracked as a separate entity entry? | Yes | No |
| Can be modeled as an independent entity root? | No | No |
| Can map to a separate table? | Yes | No |
| Can share the containing table? | Yes | Yes |
| Can map to JSON? | Yes | Yes |
| Can contain navigations to entity types? | Yes | No |
| Can the same CLR instance be shared in multiple places? | No | Yes |
| Best fit for a pure value object? | Usually not in EF Core 10 | Yes |
| Best fit when ownership and entity identity matter? | Yes | No |

For example, an `Address` that simply represents street, city, and postal information is usually a better fit for a Complex Type because the address has no identity of its own.

An owned type is more appropriate when the modeled object needs entity behavior in EF Core while still belonging to a specific owner.

> In EF Core 10, Complex Types are generally a better fit for value-object scenarios where entity identity is not meaningful. Use Owned Entity Types when ownership and entity identity are part of the model.

Choosing between an owned entity and a complex type should be based on **model semantics first**, not on whether the data will eventually be stored in columns or JSON.

Once an owned entity is the right modeling choice, JSON is one possible storage representation.

## Map an Owned Entity to JSON

Using the same `Order` and `OrderDetails` model, configure the owned navigation with `ToJson`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .OwnsOne(
            o => o.Details,
            owned =>
            {
                owned.ToJson();
            });
}
```

The ownership relationship remains the same:

```text
Order
└── OrderDetails
```

Only the storage representation changes.

With a provider that supports EF Core JSON mapping, the owned object is stored in a JSON column instead of being mapped to individual relational columns.

> **Ownership defines the model; `ToJson` changes how the owned data is stored.**

JSON mapping for owned entities remains supported in EF Core 10. However, when the JSON object represents a value rather than an entity with meaningful identity, a Complex Type is generally the better choice for a new model.

For a deeper discussion of JSON storage and provider-specific behavior, see **JSON Mapping (Coming soon)**.

## Behavior and Limitations

Because an owned type is an entity type, some behaviors differ from both regular entity roots and Complex Types.

### Owned Types Are Not Independent Entity Roots

An owned entity type cannot be configured as a regular independent entity root.

For example, an owned type cannot have its own `DbSet<T>`:

```csharp
// Not supported for an owned entity type
public DbSet<OrderDetails> OrderDetails { get; set; }
```

It also cannot be configured independently as a regular entity with:

```csharp
// Not supported for an owned entity type
modelBuilder.Entity<OrderDetails>();
```

Instead, its configuration belongs to an ownership relationship:

```csharp
modelBuilder.Entity<Order>()
    .OwnsOne(o => o.Details);
```

This reinforces the modeling rule used throughout the article: an owned entity participates in the EF Core model through its owner.

### Owned Instances Cannot Be Shared Between Owners

The same owned entity instance cannot belong to multiple owners.

Each owned instance has entity identity associated with a specific ownership, so sharing the same instance across owners does not match owned-entity semantics.

This is another important difference from Complex Types, where the same CLR instance can be used in multiple places because the complex value has no entity identity of its own.

### Owned Types Are Loaded with Their Owner

Owned navigations are loaded with the owner automatically, so an explicit `Include` is not required, including when the owned type is mapped to a separate table.

For example:

```csharp
var order = await context.Orders
    .FirstAsync(o => o.Id == orderId);

Console.WriteLine(order.Details.ReferenceNumber);
```

The physical table layout does not change this ownership behavior.

This differs from regular relationships, where related entities often need to be loaded explicitly. For more about loading regular navigations, see [Include in EF Core](/querying/include).

### Owned Types Cannot Have Inheritance Hierarchies

Owned entity types do not support inheritance hierarchies.

If the model requires polymorphic entity types with inheritance, a regular entity hierarchy is a better fit than an owned type.

### Key Limitations at a Glance

| Capability | Owned Entity |
|---|---|
| Own `DbSet<T>` | Not supported |
| Independent `ModelBuilder.Entity<T>()` configuration | Not supported |
| Share the same instance between multiple owners | Not supported |
| Inheritance hierarchy | Not supported |
| Separate table | Supported |
| Nested ownership | Supported |
| Owned collection | Supported |
| JSON mapping | Supported with a compatible provider |

Most of these differences follow from the same central idea: **an owned type is still an entity type, but it exists in the EF Core model through an ownership relationship**.

## External Resources - Owned Entity

The following videos provide practical demonstrations of Owned Entity Types from complementary perspectives. Together, they show how owned types differ from Complex Types, how `OwnsMany` and nested ownership affect identity and key design, and how the same ownership model can use different storage strategies, including JSON. The selected timestamps focus only on the sections that directly support the concepts covered in this article.

### Video 1 - EF Core 8 Finally Fixes Value Objects

<iframe width="560" height="315" src="https://www.youtube.com/embed/LhCD5CUSP6g" title="EF Core 8 Finally Fixes Value Objects" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović first configures a value object with `OwnsOne` and explains that owned types are still entities under the hood, with identity that affects tracking and instance sharing. He then contrasts this behavior with Complex Types and shows why they are often a better fit for value objects.

**Key timestamps:**

- [**1:01**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=61s) — Configures a value object as an owned type with `OwnsOne`.
- [**1:46**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=106s) — Explains that owned types are entities under the hood and have identity in the EF Core model.
- [**2:00**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=120s) — Shows why the same owned instance cannot be reused across multiple owners.
- [**2:19**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=139s) — Introduces Complex Types as an alternative for modeling value objects without entity identity.

This video is especially useful for seeing the conceptual shift from `OwnsOne` to Complex Types in a concrete example. It reinforces one of the article's central decisions: Owned Entity Types still have entity identity, while Complex Types model values without their own identity. The video targets EF Core 8, so later limitations mentioned outside these selected sections should not be treated as current EF Core 10 guidance.

### Video 2 - EF Core, DDD, and Clean Architecture - Mapping Aggregates to Relational Databases

<iframe width="560" height="315" src="https://www.youtube.com/embed/5_un3PUER8U" title="EF Core, DDD, and Clean Architecture - Mapping Aggregates to Relational Databases" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Amichai Mantinband maps a complete DDD aggregate to relational tables and uses `OwnsOne` and multiple nested `OwnsMany` configurations to model ownership at different levels. The example also shows how ownership foreign keys and composite keys are configured and later reflected in the generated migration.

**Key timestamps:**

- [**12:19**](https://www.youtube.com/watch?v=5_un3PUER8U&t=739s) — Explains why an entity inside an aggregate can use a composite key that combines its own ID with the owner's ID.
- [**27:53**](https://www.youtube.com/watch?v=5_un3PUER8U&t=1673s) — Starts configuring an owned collection with `OwnsMany`, `WithOwner`, and the ownership foreign key.
- [**30:51**](https://www.youtube.com/watch?v=5_un3PUER8U&t=1851s) — Configures nested `OwnsMany` for items owned by another owned entity.
- [**49:59**](https://www.youtube.com/watch?v=5_un3PUER8U&t=2999s) — Verifies the generated migration and confirms the composite keys and relationships produced by the ownership model.

Its main value is the progression from aggregate design to actual `OwnsMany` configuration and finally to migration verification. That makes the identity and key behavior of owned collections much easier to understand than from configuration code alone. The video is broader than this article, so the selected timestamps intentionally exclude unrelated DDD and infrastructure setup.

### Video 3 - .NET Data Community Standup - JSON Columns

<iframe width="560" height="315" src="https://www.youtube.com/embed/0W2vcYMoJ8g" title=".NET Data Community Standup - JSON Columns" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In this official .NET Data Community Standup, members of the EF Core team demonstrate JSON-column mapping and explain how it builds on the existing owned-entity model. The same owned aggregate is shown using relational table mappings and then stored as a JSON document with `ToJson()`.

**Key timestamps:**

- [**29:00**](https://www.youtube.com/watch?v=0W2vcYMoJ8g&t=1740s) — Transitions from relational table mappings to storing the owned aggregate as a JSON document.
- [**30:04**](https://www.youtube.com/watch?v=0W2vcYMoJ8g&t=1804s) — Configures `OwnsOne(...).ToJson()` and explains that the entire owned aggregate is stored as JSON.
- [**31:07**](https://www.youtube.com/watch?v=0W2vcYMoJ8g&t=1867s) — Verifies the resulting JSON-column storage, including nested owned data and collections.
- [**35:14**](https://www.youtube.com/watch?v=0W2vcYMoJ8g&t=2114s) — Explains the conceptual distinction between owned-entity semantics and the available storage strategies: same table, separate table, or JSON.

What makes this resource particularly valuable is that the explanation comes directly from the EF Core team and separates model semantics from physical storage very clearly. The same owned aggregate can retain its ownership semantics while moving between shared columns, separate tables, and a JSON document. Because the session targets EF Core 7, some limitations discussed later in the video are historical and should not be treated as current EF Core 10 behavior.

## Summary

Owned entity types model objects that belong to another entity and are managed through that owner in the EF Core model.

- Use `OwnsOne` when the owner has a single owned object.
- Use `OwnsMany` when the owner has a collection of owned objects.
- You normally do not need to add key properties just for the owned type; EF Core can create the required keys as shadow properties.
- Reference owned entities share the owner's table by default with relational providers, but they can be mapped to a separate table.
- Owned entities can be nested and can also be mapped to JSON with a compatible provider.
- Choose a Complex Type instead when the object represents a value without meaningful entity identity.

## Related Articles

Continue with these related EF Core modeling and querying topics:

- [One-to-One Relationships in EF Core](/modeling/relationships-one-to-one) — Learn how two independent entity types can be associated with at most one entity on the other side.
- [One-to-Many Relationships in EF Core](/modeling/relationships-one-to-many) — Learn how one entity can be associated with multiple dependent entities in a regular relationship.
- [Complex Type](/modeling/complex-type) — Model values without entity identity and compare them with owned entity types.
- [Include in EF Core](/querying/include) — Learn how regular related navigations are loaded and how `Include` differs from the automatic loading behavior of owned navigations.
- JSON Mapping (Coming soon) — Learn how EF Core maps structured models to JSON columns and which behaviors depend on the database provider.

## FAQ

### Does an owned entity need a primary key?

Yes, but you usually do not need to define it yourself.

Because an owned type is still an entity type in EF Core, it needs a key. EF Core normally creates the key as a shadow property when you configure the ownership relationship, so the key does not need to appear in the C# class.

With `OwnsOne`, the shadow key uses the same value as the owner's primary key. With `OwnsMany`, EF Core also needs a value that distinguishes each item within the collection.

You only need to configure the key explicitly when the default key design does not fit your model.

### Can an owned entity be stored in a separate table?

Yes.

A reference owned entity shares the owner's table by default with relational providers, but you can map it to another table with `ToTable`.

Changing the table changes the storage mapping, not the ownership relationship.

### What is the difference between an Owned Entity and a Complex Type?

The main difference is how EF Core models them.

An Owned Entity is still an entity type. EF Core gives it a key and manages it through an ownership relationship, even when the key exists only as a shadow property.

A Complex Type represents a value and has no key or entity identity of its own.

In EF Core 10, Complex Types are generally the better fit for value objects where entity identity is not meaningful.

### Can I query an owned entity independently?

No. An owned entity is not an independent entity root in the model.

You cannot expose it through its own `DbSet<T>` or configure it independently with `ModelBuilder.Entity<T>()`. It participates in the model through its owner.

### Do I need `Include` to load owned entities?

No. Owned navigations are included with their owner automatically, including when the owned type is mapped to a separate table.

This differs from regular relationships, where related entities often need to be loaded explicitly, for example with `Include`.

### Can an owned entity be mapped to JSON?

Yes, with a provider that supports EF Core JSON mapping.

Use `ToJson()` to change the storage representation while keeping the ownership relationship in the EF Core model.

For new models where the JSON object represents a value without entity identity, consider a Complex Type in EF Core 10.