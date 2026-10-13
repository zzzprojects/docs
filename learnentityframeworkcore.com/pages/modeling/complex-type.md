---
title: EF Core Complex Type
description: Learn how to configure Complex Types in EF Core, including optional properties, collections, nested types, JSON mapping, and value semantics.
canonical: /modeling/complex-type
status: Published
lastmod: 2026-10-13
---

# Complex Type in EF Core

Complex types group related properties into structured values without giving them independent entity identity. For example, an `Order` can contain shipping and billing addresses without duplicating their fields.

Unlike navigation properties, complex properties are part of their containing entity and do not require `Include()` to be loaded. For loading related entities through navigations, see [Include in EF Core](/querying/include).

## Complex Property in EF Core

Suppose an `Order` needs both a shipping address and a billing address. Instead of defining separate street, city, state, country, and postal code fields for each, you can create an `Address` type and use it for both properties.

The following example configures these addresses using the `ComplexProperty()` Fluent API:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public DateTime OrderDate { get; set; }

    public required Address ShippingAddress { get; set; }
    public required Address BillingAddress { get; set; }
}

public record Address
{
    public required string Street { get; init; }
    public required string City { get; init; }
    public required string State { get; init; }
    public required string Country { get; init; }
    public required string PostalCode { get; init; }
}

public class AppDbContext : DbContext
{
    public DbSet<Order> Orders => Set<Order>();

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=orders.db");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>(entity =>
        {
            entity.ComplexProperty(o => o.ShippingAddress);
            entity.ComplexProperty(o => o.BillingAddress);
        });
    }
}
```

`Order` is an entity identified by `OrderId`. `Address` is a complex type: it has no independent entity identity or primary key. EF Core treats each configured address as a structured property of its containing order, not as a separate entity or relationship.

Complex types are not discovered as such by default. They must be identified through `ComplexProperty()`, `[ComplexType]`, or applicable explicit model configuration, as explained later in *Configure Complex Types*.

### How Complex Properties Are Stored

By default, relational providers map a complex property's scalar members to columns in the containing entity's table.

For example, the `Orders` table contains `OrderId`, `OrderDate`, `ShippingAddress_Street`, `ShippingAddress_City`, `BillingAddress_Street`, and `BillingAddress_City`, along with columns for the other mapped address members.

By convention, EF Core prefixes column names with the complex property name. Both addresses are stored in `Orders`; no separate `Addresses` table is created.

You can save an order with both addresses using normal EF Core methods:

```csharp
await using var context = new AppDbContext();
await context.Database.EnsureCreatedAsync();

var shippingAddress = new Address
{
    Street = "Saint-Hubert",
    City = "Laval",
    State = "Quebec",
    Country = "Canada",
    PostalCode = "H7G 2Y2"
};

var order = new Order
{
    OrderDate = DateTime.UtcNow,
    ShippingAddress = shippingAddress,
    BillingAddress = shippingAddress
};

context.Orders.Add(order);
await context.SaveChangesAsync();
```

`EnsureCreatedAsync()` is convenient for this standalone example. Applications that manage schema changes through migrations should use their migration workflow instead.

Notice that `ShippingAddress` and `BillingAddress` reference the same `Address` instance.

EF Core permits this because complex values do not have independent entity identities. Both properties contribute values to their respective columns in the containing `Order`.

However, sharing a complex value does not change the normal behavior of C# references. If both properties pointed to the same mutable object, modifying that instance would affect both addresses.

The `Address` record in this example uses `init`-only string properties. To change the shipping city while preserving the original shared value, create a new record using `with`:

```csharp
order.ShippingAddress = order.ShippingAddress with
{
    City = "Montreal"
};

await context.SaveChangesAsync();
```

`ShippingAddress` now references a new record whose city is Montreal. `BillingAddress` still references the original record whose city is Laval.

EF Core detects the modified shipping-address value through the containing order.

The distinction is important: **complex types have value semantics in the EF Core model, but CLR reference and copy behavior still depend on whether you use a class, record, or struct.** We will explore those representations in *Configure Complex Types*.

## Optional Complex Types

Starting with EF Core 10, complex properties can be optional. An entity can therefore contain a structured value that is either present or `null`, without introducing a separate entity or relationship.

For example, an order might require a shipping address while allowing its billing address to be omitted.

Change the billing address declaration to `Address?`:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public DateTime OrderDate { get; set; }

    public required Address ShippingAddress { get; set; }
    public Address? BillingAddress { get; set; }
}
```

This is an alternative version of the original `Order` class. Replace that declaration when trying this example; the original `Address` record remains unchanged.

With nullable reference types enabled, EF Core recognizes `BillingAddress` as optional. The Fluent API configuration remains the same:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>(entity =>
    {
        entity.ComplexProperty(o => o.ShippingAddress);
        entity.ComplexProperty(o => o.BillingAddress);
    });
}
```

An order can now omit its billing address:

```csharp
await using var context = new AppDbContext();

var order = new Order
{
    OrderDate = DateTime.UtcNow,
    ShippingAddress = new Address
    {
        Street = "Saint-Hubert",
        City = "Laval",
        State = "Quebec",
        Country = "Canada",
        PostalCode = "H7G 2Y2"
    },
    BillingAddress = null
};

context.Orders.Add(order);
await context.SaveChangesAsync();
```

This example assumes that the database schema matches the optional model. If `orders.db` was previously created with required billing-address columns, update the schema before running this variation.

With separate-column mapping, EF Core stores `NULL` in the columns belonging to `BillingAddress`. When the order is loaded again, its billing address is materialized as `null`, rather than as an empty `Address` instance.

### How EF Core Detects an Optional Complex Value

Optional complex properties introduce a modeling challenge: how can EF Core distinguish a missing complex value from an existing object whose members are all `null`?

In EF Core 10, an optional complex property mapped to individual columns generally needs at least one member configured as required in the EF Core model. Alternatively, a discriminator can identify whether the complex value is present.

Our `Address` record has five non-nullable string members. With nullable reference types enabled, EF Core configures these members as required by convention, providing the information needed to distinguish an absent billing address from a populated one.

The C# `required` modifier and EF Core requiredness serve different purposes:

- **C# `required`** requires callers to initialize a member when constructing an object.
- **EF Core requiredness** defines whether a mapped member must have a value when its containing complex object is present. For an optional complex property mapped to separate columns, those columns can still permit `NULL` to represent the absence of the entire complex value.

The presence test therefore depends on EF Core's model configuration, not merely on the C# `required` keyword.

For complex types without a naturally required scalar member, EF Core 10 also provides `HasDiscriminator()` to distinguish an absent value from a present one. For example, when an optional `Place.Location` complex property has no required members, `ComplexProperty(p => p.Location, b => b.HasDiscriminator())` configures a required shadow discriminator without adding a CLR property. This is a separate model variant, not a configuration needed by the `Address` example.

For address models containing required fields, relying on those fields is usually simpler than introducing a separate discriminator.

### Assigning an Optional Complex Value

You can assign a billing address to a tracked order even if its previous value was `null`:

```csharp
await using var context = new AppDbContext();

var order = await context.Orders.SingleAsync(o => o.OrderId == 1);
order.BillingAddress = new Address
{
    Street = "Main Street",
    City = "Montreal",
    State = "Quebec",
    Country = "Canada",
    PostalCode = "H3A 1A1"
};

await context.SaveChangesAsync();
```

This assumes order 1 exists and initially has no billing address. EF Core detects the assignment through the tracked `Order` and updates its billing-address columns; no separate address entity is inserted.

**Version note:** Optional complex properties were introduced in EF Core 10. EF Core 8 and 9 require complex properties to be non-null.

## Complex Collections

An individual complex property represents one structured value. Starting with EF Core 10, an entity can also contain a collection of complex values.

For example, a distributor may operate several shipping centers, each described by an address. When those addresses do not require independent entity identities, they can be represented as a complex collection.

The following example reuses the original `Address` record and configures `ShippingCenters` with `ComplexCollection()`:

```csharp
public class Distributor
{
    public int Id { get; set; }
    public required string Name { get; set; }

    public List<Address> ShippingCenters { get; set; } = new();
}

public class AppDbContext : DbContext
{
    public DbSet<Distributor> Distributors => Set<Distributor>();

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=distributors.db");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Distributor>()
            .ComplexCollection(
                d => d.ShippingCenters,
                collection => collection.ToJson());
    }
}
```

This is a separate example model with its own `AppDbContext` configuration, not a second context definition to include alongside the original order example. Treat each alternative model in this article as an independent scenario with a matching database schema.

`ComplexCollection()` identifies `ShippingCenters` as a collection of complex values rather than related entities. `ToJson()` configures its storage as a JSON document.

In EF Core 10, relational providers require JSON mapping for complex collections. Their elements are not mapped to independent rows in a separate relational table.

### Saving a Complex Collection

You can populate the collection when creating a distributor:

```csharp
await using var context = new AppDbContext();
await context.Database.EnsureCreatedAsync();

var distributor = new Distributor
{
    Name = "Quebec Distribution",
    ShippingCenters = new List<Address>
    {
        new()
        {
            Street = "Saint-Hubert",
            City = "Laval",
            State = "Quebec",
            Country = "Canada",
            PostalCode = "H7G 2Y2"
        },
        new()
        {
            Street = "Main Street",
            City = "Montreal",
            State = "Quebec",
            Country = "Canada",
            PostalCode = "H3A 1A1"
        }
    }
};

context.Distributors.Add(distributor);
await context.SaveChangesAsync();
```

The `Distributors` table stores the collection in a single `ShippingCenters` column.

Its JSON content can have the following structure:

```json
[
  {
    "Street": "Saint-Hubert",
    "City": "Laval",
    "State": "Quebec",
    "Country": "Canada",
    "PostalCode": "H7G 2Y2"
  },
  {
    "Street": "Main Street",
    "City": "Montreal",
    "State": "Quebec",
    "Country": "Canada",
    "PostalCode": "H3A 1A1"
  }
]
```

This JSON is illustrative. The physical column type and exact serialization details depend on the provider.

When you query the distributor, EF Core materializes its complex collection along with the containing entity:

```csharp
await using var context = new AppDbContext();

var distributor = await context.Distributors
    .SingleAsync(d => d.Name == "Quebec Distribution");

foreach (var center in distributor.ShippingCenters)
{
    Console.WriteLine($"{center.City}, {center.Country}");
}
```

The collection elements do not have separate entity identities, and no `Include()` is required to load them.

One important EF Core 10 limitation is that **complex collection elements must be reference types**, such as classes or records. Collections of complex `struct` or `record struct` values are not supported.

Owned collections configured with `OwnsMany()` have different identity and mapping semantics. We will compare these approaches in *Complex Type or Owned Entity?*.

## Nested Complex Types

Collections allow an entity to contain multiple complex values. Nesting solves a different modeling problem: a complex type can itself contain other complex types.

For example, an `Address` can contain `HomePhone` and `WorkPhone` values. Each phone number has its own structure, but neither requires an entity identity or relationship.

To extend the original example, replace the initial `Address` declaration with the following version and add a `PhoneNumber` type:

```csharp
public record PhoneNumber
{
    public required string AreaCode { get; init; }
    public required string Number { get; init; }
}

public record Address
{
    public required string Street { get; init; }
    public required string City { get; init; }
    public required string State { get; init; }
    public required string Country { get; init; }
    public required string PostalCode { get; init; }

    public required PhoneNumber HomePhone { get; init; }
    public required PhoneNumber WorkPhone { get; init; }
}
```

The `Order` entity continues to contain `ShippingAddress` and `BillingAddress`, but each address now includes two nested complex properties.

Configure both levels using Fluent API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>(entity =>
    {
        entity.ComplexProperty(
            o => o.ShippingAddress,
            address =>
            {
                address.ComplexProperty(a => a.HomePhone);
                address.ComplexProperty(a => a.WorkPhone);
            });

        entity.ComplexProperty(
            o => o.BillingAddress,
            address =>
            {
                address.ComplexProperty(a => a.HomePhone);
                address.ComplexProperty(a => a.WorkPhone);
            });
    });
}
```

The outer `ComplexProperty()` calls configure the two addresses. The nested calls identify the phone numbers as complex properties within each address.

### How Nested Complex Types Are Stored

With separate-column mapping, EF Core stores the scalar members of nested complex types in the containing `Orders` table.

The conventional column names reflect their complete property paths:

| Property path                        | Column                               |
| ------------------------------------ | ------------------------------------ |
| `ShippingAddress.HomePhone.AreaCode` | `ShippingAddress_HomePhone_AreaCode` |
| `ShippingAddress.HomePhone.Number`   | `ShippingAddress_HomePhone_Number`   |
| `ShippingAddress.WorkPhone.Number`   | `ShippingAddress_WorkPhone_Number`   |
| `BillingAddress.HomePhone.Number`    | `BillingAddress_HomePhone_Number`    |

**EF Core 10 naming change:** Nested complex properties now use the full property path in their conventional column names. In EF Core 8 and 9, these columns used shorter names based on the declaring complex type. When upgrading an existing relational schema, review the generated migration to ensure column renames and existing data are handled correctly.

The original address fields, such as `ShippingAddress_City`, remain in the same table.

For example, a shipping address with nested phone numbers can be initialized as follows:

```csharp
var shippingAddress = new Address
{
    Street = "Saint-Hubert",
    City = "Laval",
    State = "Quebec",
    Country = "Canada",
    PostalCode = "H7G 2Y2",

    HomePhone = new PhoneNumber
    {
        AreaCode = "450",
        Number = "5550101"
    },
    WorkPhone = new PhoneNumber
    {
        AreaCode = "514",
        Number = "5550102"
    }
};
```

To save this value, assign it to `Order.ShippingAddress` and provide a `BillingAddress` with its required `HomePhone` and `WorkPhone` members. Both nested phone numbers are then persisted through the containing `Order`, not as separate entities.

## Configure Complex Types

The previous examples used Fluent API to identify complex properties. EF Core also supports Data Annotations, allowing you to configure complex values according to how they are used in your model.

Configuration and CLR representation answer different questions: EF Core configuration determines how a complex value is mapped, while the choice of class, record, or struct determines its behavior in C#.

### Configure a Complex Type with Data Annotations

You can identify a CLR type as complex using `[ComplexType]`:

```csharp
using System.ComponentModel.DataAnnotations.Schema;

[ComplexType]
public record Address
{
    public required string Street { get; init; }
    public required string City { get; init; }
    public required string State { get; init; }
    public required string Country { get; init; }
    public required string PostalCode { get; init; }
}
```

This returns to the original `Address` definition containing five scalar properties, without the nested phone numbers introduced in the previous section.

The original `Order` entity can continue using its `ShippingAddress` and `BillingAddress` properties unchanged.

With `[ComplexType]` applied to `Address`, EF Core recognizes its uses as complex properties without requiring individual Fluent API calls solely to identify them.

This approach is useful when the same CLR type should consistently be treated as complex throughout the model. Additional Fluent API configuration remains available when you need to customize a particular use.

### Configure a Complex Type with Fluent API

Fluent API keeps configuration in `OnModelCreating()` and allows different uses of the same complex type to have different mapping details.

For example, you can customize the column name and maximum length of selected address members:

```csharp
modelBuilder.Entity<Order>()
    .ComplexProperty(
        o => o.ShippingAddress,
        address =>
        {
            address.Property(a => a.Street)
                .HasColumnName("ShippingStreet")
                .HasMaxLength(150);

            address.Property(a => a.PostalCode)
                .HasMaxLength(20);
        });
```

This maps `ShippingAddress.Street` to `ShippingStreet` instead of the conventional `ShippingAddress_Street`.

`HasMaxLength()` configures the maximum length of the selected members. Other properties retain their conventional mappings unless configured separately.

Unlike `[ComplexType]`, which identifies the CLR type, the `ComplexProperty()` configuration can target a specific property and customize its mapping.

### Required Complex Properties

EF Core also allows you to configure whether a complex property is required.

For example:

```csharp
modelBuilder.Entity<Order>()
    .ComplexProperty(
        o => o.ShippingAddress,
        address => address.IsRequired());
```

In the original model, `ShippingAddress` is a non-nullable reference property. With nullable reference types enabled, EF Core already treats it as required by convention. The explicit configuration is useful when you want to express that requirement directly.

Several mechanisms influence complex-type configuration, but they serve different purposes:

| Mechanism                     | Purpose                                                   |
| ----------------------------- | --------------------------------------------------------- |
| `[ComplexType]`               | Identifies a CLR type as complex                          |
| `ComplexProperty()`           | Configures an individual complex property                 |
| `[Required]` / `IsRequired()` | Expresses requiredness in the EF Core model               |
| C# `required`                 | Requires initialization when constructing an object in C# |

For optional complex properties, see *Optional Complex Types* for presence detection using required members or a discriminator.

### Reference Types: Class and Record

A complex type can be a mutable `class` or a `record`. Both are reference types, so assigning the same instance to two properties shares that object. EF Core allows the sharing, but C# determines what happens when the object changes.

For example, replace the original record with a mutable class whose members have `set` accessors:

```csharp
public class Address
{
    public required string Street { get; set; }
    public required string City { get; set; }
    public required string State { get; set; }
    public required string Country { get; set; }
    public required string PostalCode { get; set; }
}
```

When the same `Address` instance is assigned to both properties, the following operations affect both addresses:

```csharp
var address = new Address
{
    Street = "Saint-Hubert",
    City = "Laval",
    State = "Quebec",
    Country = "Canada",
    PostalCode = "H7G 2Y2"
};

var order = new Order
{
    OrderDate = DateTime.UtcNow,
    ShippingAddress = address,
    BillingAddress = address
};

address.City = "Montreal";

Console.WriteLine(order.ShippingAddress.City); // Montreal
Console.WriteLine(order.BillingAddress.City);  // Montreal
```

This example demonstrates shared CLR references, not a separate EF Core tracking mechanism.

By contrast, the original `Address` record uses `init`-only string members. Its `with` expression, demonstrated in *Complex Property in EF Core*, creates a new record rather than mutating the shared instance. Records are not inherently immutable: records with setters or mutable members can still expose mutable state.

**Choose the CLR representation based on the intended sharing and mutation behavior**, not on EF Core configuration alone.

### Value Types: Struct and Record Struct

Complex properties have supported CLR value types, including `struct` and `record struct`, since EF Core 8.

Unlike reference types, structs are copied when assigned to another property. This avoids sharing a single object reference, although the behavior of any reference-type members inside the struct still matters.

For example, replace the original `Address` record with this alternative `readonly record struct`:

```csharp
public readonly record struct Address(
    string Street,
    string City,
    string State,
    string Country,
    string PostalCode);
```

It can use the same `ComplexProperty()` configuration as the original reference-type model.

Create an address and assign it to both order properties:

```csharp
var address = new Address(
    "Saint-Hubert",
    "Laval",
    "Quebec",
    "Canada",
    "H7G 2Y2");

var order = new Order
{
    OrderDate = DateTime.UtcNow,
    ShippingAddress = address,
    BillingAddress = address
};

order.ShippingAddress = order.ShippingAddress with
{
    City = "Montreal"
};

Console.WriteLine(order.ShippingAddress.City); // Montreal
Console.WriteLine(order.BillingAddress.City);  // Laval
```

Each order property initially receives its own copy of the address value. Replacing `ShippingAddress` therefore leaves `BillingAddress` unchanged.

A `readonly record struct` prevents reassignment of its members after construction. An ordinary mutable `struct`, however, can expose setters. Being a value type does not automatically make its data immutable.

The main differences are:

| CLR representation                             | Assignment behavior          | Modification behavior                                           |
| ---------------------------------------------- | ---------------------------- | --------------------------------------------------------------- |
| Mutable `class`                                | Shares a reference           | Mutating the instance affects all references to it              |
| `record class` with `init`-only scalar members | Initially shares a reference | `with` creates a new instance                                   |
| `readonly record struct`                       | Copies the value             | `with` creates a modified value without changing the other copy |

Immutable representations are often useful for value objects, but the appropriate choice depends on the domain, the size and structure of the type, and its intended C# behavior.

**Version distinction:** Structs and record structs have been supported as individual complex properties since EF Core 8. EF Core 10 adds complex collections, but their elements must still be reference types; collections of complex structs are not supported.

## Complex Type or Owned Entity?

Complex types and owned entity types can both represent data contained within another entity. They can share the containing table and support JSON mapping, but they differ fundamentally in identity, ownership, and tracking semantics.

A complex type represents structured values without independent entity identity. An owned entity remains an entity type with a key and an ownership relationship, even when that key is maintained through shadow properties.

### When Should You Use Each Type?

Consider the `Address` model used throughout this article.

Its street, city, state, country, and postal code describe a value. If that value does not need an identity or lifecycle of its own, a complex type is a natural choice.

An owned entity may be more appropriate when the model requires entity identity within an owner, navigations to other entities, or separate-table mapping. When an object needs to exist and be managed independently of any owner, a regular entity is generally more suitable.

The following table compares identity and lifecycle first, followed by mapping and configuration capabilities in EF Core 10:

| Question                                                                   | Complex Type                              | Owned Entity                                     |
| -------------------------------------------------------------------------- | ----------------------------------------- | ------------------------------------------------ |
| Has identity in the EF Core model?                                         | No                                        | Yes                                              |
| Has a primary key?                                                         | No                                        | Yes, including implicit or shadow keys           |
| Tracked as a separate entity entry?                                        | No                                        | Yes                                              |
| Can be queried through its own `DbSet<T>`?                                 | No                                        | No                                               |
| Can exist as an independent entity root in this mapping?                   | No                                        | No                                               |
| Can share the containing table?                                            | Yes                                       | Yes                                              |
| Can map to a separate table?                                               | No                                        | Yes                                              |
| Can map to JSON?                                                           | Yes, with supported providers             | Yes, with supported providers                    |
| Supports collections?                                                      | Yes, using JSON with relational providers | Yes, using `OwnsMany()`                          |
| Can contain navigations to entity types?                                   | No                                        | Yes, subject to relationship and ownership rules |
| Can the same reference-type instance be shared across multiple properties? | Yes; mutable sharing requires care        | No, not as multiple tracked owned instances      |
| Supports `struct` and `record struct`?                                     | Yes, since EF Core 8                      | No                                               |
| Suitable for a value object without identity?                              | Usually preferred                         | Possible, but introduces entity semantics        |

The difference is especially important for collections.

An owned collection configured with `OwnsMany()` consists of owned entities with identity within their owner. A complex collection configured with `ComplexCollection()` contains values without independent keys and requires JSON mapping with relational providers in EF Core 10.

Tracking also follows these distinct semantics. Owned entities have their own entity entries; complex values participate in change detection through the entities that contain them.

**Choose based on the identity and lifecycle required by the model, rather than choosing solely according to whether the values will be stored in columns or JSON.**

EF Core 10 also supports direct updates involving complex values through `ExecuteUpdateAsync()` in supported scenarios, including certain JSON-mapped operations. These updates depend on the mapping and provider; the full update workflow belongs to the linked Saving article.

For a detailed treatment of ownership, keys, `OwnsOne()`, and `OwnsMany()`, see Owned Entity Types in EF Core (Coming soon). For direct database updates without loading tracked entities, see [ExecuteUpdate in EF Core](/saving/execute-update).

## Map Complex Types to JSON

The earlier examples mapped the members of individual complex properties to separate relational columns. EF Core 10 also supports mapping a complex property to a JSON document using `ToJson()`.

This changes how the value is stored without changing its CLR representation or introducing independent entity identity.

### Configure a Complex Property with ToJson()

Return to the original `Order` and `Address` model: both addresses are required, and `Address` contains the five scalar members without nested phone numbers.

To store each address as JSON, configure:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>(entity =>
    {
        entity.ComplexProperty(
            o => o.ShippingAddress,
            address => address.ToJson());

        entity.ComplexProperty(
            o => o.BillingAddress,
            address => address.ToJson());
    });
}
```

`ComplexProperty()` identifies each address as complex, while `ToJson()` selects the JSON storage representation.

This replaces the separate-column mapping used earlier. It is not an additional configuration to apply on top of that mapping.

If an existing database stores address members in separate columns, changing `OnModelCreating()` alone will not migrate the data. The schema and existing values require an appropriate migration strategy.

### How JSON Mapping Changes the Table

With conventional separate-column mapping, address members occupy columns such as `ShippingAddress_Street` and `BillingAddress_City`.

After applying `ToJson()` to both addresses, the logical `Orders` table instead contains:

| Column            | Stored value                   |
| ----------------- | ------------------------------ |
| `OrderId`         | Order primary key              |
| `OrderDate`       | Order date                     |
| `ShippingAddress` | Shipping address JSON document |
| `BillingAddress`  | Billing address JSON document  |

For example, the `ShippingAddress` document can contain:

```json
{
  "Street": "Saint-Hubert",
  "City": "Laval",
  "State": "Quebec",
  "Country": "Canada",
  "PostalCode": "H7G 2Y2"
}
```

This representation is illustrative. The physical database column type and serialization details depend on the provider.

The key modeling distinction remains unchanged: `ShippingAddress` is still a complex value belonging to `Order`, even though its members are now stored together in a JSON document.

### Saving and Querying JSON-Mapped Complex Types

Once the JSON mapping and corresponding database schema are in place, you can save an order using ordinary EF Core operations:

```csharp
await using var context = new AppDbContext();

var order = new Order
{
    OrderDate = DateTime.UtcNow,

    ShippingAddress = new Address
    {
        Street = "Saint-Hubert",
        City = "Laval",
        State = "Quebec",
        Country = "Canada",
        PostalCode = "H7G 2Y2"
    },

    BillingAddress = new Address
    {
        Street = "Main Street",
        City = "Montreal",
        State = "Quebec",
        Country = "Canada",
        PostalCode = "H3A 1A1"
    }
};

context.Orders.Add(order);
await context.SaveChangesAsync();

var orderId = order.OrderId;
```

EF Core handles JSON serialization according to the configured mapping. This normal persistence workflow does not require manual JSON serialization or raw SQL.

To verify that the saved values can be retrieved, query the order using a new context:

```csharp
await using var readContext = new AppDbContext();

var savedOrder = await readContext.Orders
    .AsNoTracking()
    .SingleAsync(o => o.OrderId == orderId);

Console.WriteLine(savedOrder.ShippingAddress.City); // Laval
Console.WriteLine(savedOrder.BillingAddress.City);  // Montreal
```

The new `DbContext` provides a separate query environment, while `AsNoTracking()` prevents the returned order from being tracked by that context.

You can also filter entities through scalar members stored inside their JSON documents:

```csharp
await using var queryContext = new AppDbContext();

var lavalOrders = await queryContext.Orders
    .Where(o => o.ShippingAddress.City == "Laval")
    .ToListAsync();
```

With EF Core 10 and SQLite, this predicate can be translated into provider-specific JSON operations. Other providers may generate different SQL and support different JSON operations.

JSON-mapped complex values also participate in normal tracked updates through `SaveChangesAsync()`. EF Core 10 additionally supports certain direct updates to JSON-mapped complex values through `ExecuteUpdateAsync()`, subject to provider and mapping capabilities.

### JSON Mapping and Complex Collections

For an individual complex property, `ToJson()` is an alternative to separate-column mapping. For a relational complex collection such as `Distributor.ShippingCenters`, JSON mapping is required in EF Core 10, as demonstrated in *Complex Collections*.

Physical JSON column types and supported operations depend on the provider and database version. Indexes, performance, and advanced migration strategies belong to a more specialized JSON mapping guide.

## Querying and Saving Complex Types

Complex values are queried and persisted through their containing entities, whether their members use separate relational columns or supported JSON mapping.

This section returns to the original `Order` and `Address` model with separate-column mapping to demonstrate entity materialization, LINQ queries, value comparisons, and property-level change detection.

The following examples use the original `Order` model with separate-column mapping. Some snippets continue using the `context` declared earlier, while others create a new context. Examples that query `OrderId == 1` assume a matching order already exists, and change-tracking examples require its shipping city to be Laval before running.

### Querying Complex Types

When EF Core materializes a complete entity, its mapped complex properties are materialized as part of that entity.

For example:

```csharp
await using var context = new AppDbContext();

var order = await context.Orders
    .FirstAsync(o => o.OrderId == 1);

Console.WriteLine(order.ShippingAddress.City);
Console.WriteLine(order.BillingAddress.City);
```

With separate-column mapping, a complete `Order` query reads address members from the `Orders` table without joining a separate address table. The generated SQL depends on the provider.

However, a query does not need to retrieve every address member when it selects only particular values.

For example, filter orders by shipping country and project their shipping cities:

```csharp
var shippingCities = await context.Orders
    .Where(o => o.ShippingAddress.Country == "Canada")
    .Select(o => new
    {
        o.OrderId,
        ShippingCity = o.ShippingAddress.City
    })
    .ToListAsync();
```

This projection retrieves the selected values without materializing complete `Order` entities.

Other standard LINQ operations, such as `OrderBy(o => o.ShippingAddress.City)`, can also use mapped complex members when supported by the provider.

### Comparing Complex Properties by Value

A more distinctive capability is comparing complete complex properties in a LINQ query.

For example, find orders whose shipping and billing addresses contain the same values:

```csharp
var ordersWithSameAddress = await context.Orders
    .Where(o => o.ShippingAddress == o.BillingAddress)
    .ToListAsync();
```

EF Core compares the mapped members of the two complex properties rather than looking for matching entity keys, because complex types do not have independent identity.

The generated SQL and supported translations may differ across providers and mappings.

This behavior is also distinct from ordinary C# equality outside a database query. A record normally implements value-based equality, while a class without an equality override generally uses reference equality.

### Saving Complex Types

The first example in *Complex Property in EF Core* demonstrated inserting an `Order` together with its shipping and billing addresses using `SaveChangesAsync()`.

For separate-column mapping, SQL Server can insert the address members as part of the containing entity's row:

```sql
INSERT INTO [Orders] (
    [OrderDate],
    [BillingAddress_City],
    [BillingAddress_Country],
    [BillingAddress_PostalCode],
    [BillingAddress_State],
    [BillingAddress_Street],
    [ShippingAddress_City],
    [ShippingAddress_Country],
    [ShippingAddress_PostalCode],
    [ShippingAddress_State],
    [ShippingAddress_Street]
)
OUTPUT INSERTED.[OrderId]
VALUES (
    @p0, @p1, @p2, @p3, @p4, @p5,
    @p6, @p7, @p8, @p9, @p10
);
```

This example illustrates the SQL Server insert shape for separately mapped complex properties. EF Core supplies the parameter values, and `OUTPUT INSERTED.[OrderId]` returns the generated key. The exact SQL, including parameter names and generated-key handling, can vary by EF Core version and provider.

The important modeling consequence is that EF Core persists the address members as part of the `Order`. It does not insert separately tracked address entities.

Updating an existing order introduces another behavior: EF Core can detect changes to individual members of a complex value.

### Updating a Complex Property

Suppose a tracked order has Laval as its shipping city, and you want to change it to Montreal.

Using the original `Address` record with `init`-only scalar members, replace the shipping address with a modified value:

```csharp
await using var context = new AppDbContext();

var order = await context.Orders
    .SingleAsync(o => o.OrderId == 1);

// The existing shipping city is Laval.
order.ShippingAddress = order.ShippingAddress with
{
    City = "Montreal"
};

await context.SaveChangesAsync();
```

If the original city was Laval and no other mapped property changed, EF Core can detect that only `ShippingAddress.City` has a different value.

With separate-column mapping, a corresponding SQL Server update may resemble:

```sql
UPDATE [Orders]
SET [ShippingAddress_City] = @city
WHERE [OrderId] = @orderId;
```

This is an illustrative SQL shape, not a captured SQL Server statement. Actual commands can include additional provider-specific clauses, generated values, or concurrency conditions.

Replacing the `Address` instance does not necessarily cause every address column to be updated. Under normal snapshot change tracking, EF Core compares mapped values and can identify the individual members that changed.

This behavior depends on the entity's tracking state and the update workflow. For example, marking an entire disconnected entity as modified can result in a broader update.

For the general persistence workflow, see [SaveChanges in EF Core](/saving/save-changes).

### Inspecting Changes to Complex Properties

Although `Address` has no independent tracked entity entry, EF Core exposes its mapped members through the containing `Order` entry.

The following example verifies the original city before modifying it, so the expected tracking results are clear:

```csharp
await using var context = new AppDbContext();

var order = await context.Orders
    .SingleAsync(o => o.OrderId == 1);

if (order.ShippingAddress.City != "Laval")
{
    throw new InvalidOperationException(
        "This example requires an order with Laval as its shipping city.");
}

order.ShippingAddress = order.ShippingAddress with
{
    City = "Montreal"
};

context.ChangeTracker.DetectChanges();

var addressEntry = context.Entry(order)
    .ComplexProperty(o => o.ShippingAddress);

var cityEntry = addressEntry.Property(a => a.City);
var streetEntry = addressEntry.Property(a => a.Street);

Console.WriteLine(cityEntry.CurrentValue); // Montreal
Console.WriteLine(cityEntry.IsModified);    // True
Console.WriteLine(streetEntry.IsModified);  // False
```

`ComplexProperty()` accesses the complex value through its containing entity entry, while `Property()` accesses an individual mapped scalar member.

`DetectChanges()` explicitly performs change detection before inspection. Because the original shipping city was Laval, changing it to Montreal marks `City` as modified. The unchanged `Street` member remains unmodified.

This demonstrates how complex values participate in change tracking without being tracked as independent entities.

For a broader explanation of original values, entity states, and property modifications, see [Change Tracker in EF Core](/saving/change-tracker).

## Behavior and Limitations

The main restrictions follow from the same principle: **a complex type is a value within an entity, not an independently identified entity**. The comparison in *Complex Type or Owned Entity?* explains the resulting differences in keys, navigation properties, tracking entries, and table mapping.

A few additional boundaries matter when applying these models:

- **Optional values:** With separate-column mapping, EF Core 10 must be able to distinguish an absent complex value from a present one, using a required member or a configured discriminator.
- **Collections:** Relational complex collections require JSON mapping; their elements must be reference types, not complex structs.
- **Queries and updates:** LINQ translations and JSON update capabilities vary by provider and database version. Complex values can participate in change tracking, but they do not have their own `DbSet<T>` or independent entity entries.
- **Shared instances:** A mutable reference-type value assigned to multiple properties can produce unexpected changes; choose its CLR representation accordingly.

### Version and Provider Considerations

Complex types were introduced in EF Core 8, with support for classes, records, structs, and record structs as individual complex properties. EF Core 10 added optional complex properties, complex collections, and JSON mapping for complex properties. Complex struct collections remain unsupported in EF Core 10.

The SQL Server `INSERT` illustrates relational column mapping, not a universal SQL command. SQL syntax, JSON support, and update behavior depend on the provider and database version. For ownership and separate-table alternatives, see Owned Entity Types in EF Core (Coming soon).

## External Resources - Complex Type

The following videos complement this article from three perspectives: an introduction to configuring Complex Types and sharing value objects, an in-depth explanation of value semantics and change tracking from the EF Core team, and a practical demonstration of JSON mapping in EF Core 10. The selected timestamps focus on relevant examples rather than unrelated portions of the recordings, with version notes where EF Core 8 limitations have changed.

### Video 1 - EF Core 8 Finally Fixes Value Objects

<iframe width="560" height="315" src="https://www.youtube.com/embed/LhCD5CUSP6g" title="EF Core 8 Finally Fixes Value Objects" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović demonstrates how Complex Types provide a more natural way to model value objects in EF Core. He configures a complex property using Fluent API, examines its relational column mapping, and shows how the same instance can be used across multiple entities without the identity restrictions associated with owned entity types. The examples also demonstrate how complex members can be accessed through LINQ queries.

**Key timestamps:**

- [**2:19**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=139s) — Introduces Complex Types and demonstrates configuration using `[ComplexType]` and `ComplexProperty()`, including how their members map to relational columns.
- [**5:03**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=303s) — Begins a demonstration of sharing one complex-type instance across multiple entities, followed by the effects of modifying that shared object.
- [**8:39**](https://www.youtube.com/watch?v=LhCD5CUSP6g&t=519s) — Demonstrates LINQ projections and filtering through members of a complex type.

This video is particularly useful for connecting the initial configuration examples with the practical behavior of shared complex values. It helps explain why Complex Types are appropriate for value objects and why their CLR mutability still matters. The video targets EF Core 8, so its later statement that complex collections are unsupported is historical; EF Core 10 supports complex collections using JSON mapping with relational providers.

### Video 2 - .NET Data Community Standup - Complex types as value objects in EF8

<iframe width="560" height="315" src="https://www.youtube.com/embed/H-soJYqWSds" title=".NET Data Community Standup - Complex types as value objects in EF8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In this official .NET Data Community Standup, Arthur Vickers and Shay Rojansky demonstrate Complex Types using customers, orders, and addresses. Their examples closely match the model used in this article, including shipping and billing addresses, shared reference-type instances, immutable records, and nested values. They also explain how EF Core compares complex values and detects changes without treating them as independent entities.

**Key timestamps:**

- [**28:48**](https://www.youtube.com/watch?v=H-soJYqWSds&t=1728s) — Demonstrates how modifying a shared mutable address affects multiple entities and why immutable representations help prevent unintended updates.
- [**33:27**](https://www.youtube.com/watch?v=H-soJYqWSds&t=2007s) — Demonstrates that replacing an immutable complex object does not necessarily update every mapped column: EF Core detects the changed scalar member and updates only that value.
- [**1:02:31**](https://www.youtube.com/watch?v=H-soJYqWSds&t=3751s) — Explains how EF Core translates equality between complex values into comparisons of their mapped members, rather than entity keys.
- [**1:12:32**](https://www.youtube.com/watch?v=H-soJYqWSds&t=4352s) — Demonstrates accessing nested complex properties through the change-tracking API and inspecting or modifying their mapped values.

The main value of this resource is its explanation of how Complex Types behave beyond their initial configuration. It connects immutability, structural comparisons, and property-level tracking with the underlying SQL operations, making it particularly relevant to the *Configure Complex Types* and *Querying and Saving Complex Types* sections. Because the session was recorded for EF Core 8, its limitations concerning optional complex properties, complex collections, and JSON mapping should not be treated as current EF Core 10 guidance.

### Video 3 - EF Core 10 Is INSANE - Overview of Game Changer features

<iframe width="560" height="315" src="https://www.youtube.com/embed/Qv1vvJ-_82c" title="EF Core 10 Is INSANE - Overview of Game Changer features" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz Zalewski demonstrates JSON mapping for Complex Types in EF Core 10 using a `Book` entity with a structured `BookAttributes` property. He configures the property with `ComplexProperty()` and `ToJson()`, examines its JSON storage, and then uses LINQ to filter books by a member stored inside the JSON document.

**Key timestamps:**

- [**4:56**](https://www.youtube.com/watch?v=Qv1vvJ-_82c&t=296s) — Configures the `BookAttributes` complex property with `ComplexProperty()` and `ToJson()` to store its members in a JSON column.
- [**5:18**](https://www.youtube.com/watch?v=Qv1vvJ-_82c&t=318s) — Examines the resulting JSON data stored in the database.
- [**5:42**](https://www.youtube.com/watch?v=Qv1vvJ-_82c&t=342s) — Builds a LINQ query that filters books by `BookAttributes.Pages`, a member mapped inside the JSON document.
- [**6:36**](https://www.youtube.com/watch?v=Qv1vvJ-_82c&t=396s) — Executes the query and verifies that the returned books satisfy the JSON-member filter.

This resource adds an EF Core 10 perspective to the two earlier videos by connecting `ToJson()` configuration with actual JSON storage and LINQ filtering. Only the JSON segment is relevant to this article; the remaining chapters address unrelated EF Core features. Optional complex properties and `ComplexCollection()` are not demonstrated.

## Summary

Complex Types represent structured values that belong to an entity without having their own identity or primary key. They are a natural fit for value objects such as addresses, contact information, and other data whose meaning comes from its values rather than an independent lifecycle.

- Use `ComplexProperty()` or `[ComplexType]` to configure a complex type. Its members are mapped as part of the containing entity, typically to columns in the same table.
- Choose between classes, records, and structs according to the value's intended behavior. Immutable records are often a good fit, especially when values may be shared between entities.
- EF Core 10 adds support for optional complex properties, complex collections, and mapping complex properties to JSON. Relational complex collections require JSON mapping.
- Complex values can participate in LINQ queries and change tracking, while `SaveChanges()` persists detected changes through their containing entities.

Choose Complex Types when the data should behave as a value within its containing entity. Use entity or owned-entity mapping when the model requires entity identity or ownership-specific behavior.

## Related Articles

- **Owned Entity Types (Coming soon)** — Compare Complex Types with owned entities, including identity, ownership, and mapping choices.
- **[Change Tracker](/saving/change-tracker)** — Learn how EF Core detects and manages changes to entity properties, including complex values.
- **[SaveChanges](/saving/save-changes)** — Understand how changes to tracked entities are persisted to the database.
- **[ExecuteUpdate](/saving/execute-update)** — Learn when to update mapped values directly in the database instead of relying on tracked changes and `SaveChanges()`.
- **[Include](/querying/include)** — Understand navigation loading and why complex properties do not require `Include()`.

## FAQ

### Do complex types need a primary key?

No. A complex type has no independent entity identity and does not require a primary key. Its values are stored as part of the containing entity rather than tracked as a separate entity.

### Can a complex type be stored in a separate table?

Not through complex-type mapping itself. In a relational database, complex values are mapped to columns in their containing entity's table or stored as JSON. If the data needs its own table, an owned entity or a regular entity may be more appropriate, depending on its identity and relationship requirements.

### Can a complex property be null?

Yes, starting with EF Core 10. An optional complex property can be `null` as a whole, rather than requiring an instance with nullable members. For separate-column relational mapping, EF Core needs a way to distinguish an absent complex value from a present one. This can be achieved with a required scalar member or an explicitly configured discriminator. EF Core 8 and 9 do not support optional complex properties.

### Do complex types require `Include()` to be loaded?

No. Complex properties are not navigation properties. When EF Core materializes an entity, its mapped complex values are materialized as part of that entity without requiring `Include()`. Queries that project only selected members can retrieve a narrower set of data.

### Can complex types be queried or updated independently?

You can filter, project, and compare complex values and their members using supported LINQ expressions, but a complex type is not an independently queryable entity with its own `DbSet`. Changes to its mapped members are normally persisted through the containing entity, while supported `ExecuteUpdateAsync()` operations can update mapped values directly in the database without loading or tracking those entities.
