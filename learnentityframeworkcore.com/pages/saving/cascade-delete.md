---
title: Cascade Delete
description: An examination of how cascade delete works in Entity Framework Core when deleting entities with related data
canonical: /saving/cascade-delete
status: Published
lastmod: 2026-06-09
---

# EF Core Cascade Delete

Cascade delete refers to how Entity Framework Core handles dependent entities when a related principal entity is deleted.

Depending on the configured delete behavior, EF Core can:

* delete dependent entities
* set foreign key values to `null`
* reject the delete operation
* take no automatic action

This article explains the available delete behaviors and how to control what happens to related entities when [`SaveChangesAsync()`](/saving/save-changes) runs.

Delete behavior is configured on a relationship by using the `OnDelete()` method:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId)
        .OnDelete(DeleteBehavior.Cascade);
}
```

The `DeleteBehavior` value determines what happens to related entities when the principal entity is deleted.

| Delete Behavior  | Result                                  |
| ---------------- | --------------------------------------- |
| `Cascade`        | Deletes dependent entities              |
| `ClientCascade`  | Deletes dependent entities in EF Core   |
| `SetNull`        | Sets foreign keys to `null`             |
| `ClientSetNull`  | Sets foreign keys to `null` in EF Core  |
| `Restrict`       | Requires dependents to be handled first |
| `NoAction`       | Requires dependents to be handled first |
| `ClientNoAction` | Takes no automatic action               |

## Delete Behavior Overview (SQL Server)

The following table summarizes how each delete behavior works in SQL Server.

The result can differ depending on whether dependent entities are tracked by the current `DbContext`.

| Delete Behavior  | Database Rule                    | Dependents Not Loaded       | Dependents Loaded          |
| ---------------- | -------------------------------- | --------------------------- | -------------------------- |
| `Cascade`        | `ON DELETE CASCADE`              | Database deletes dependents | EF Core deletes dependents |
| `ClientCascade`  | None                             | Delete fails                | EF Core deletes dependents |
| `SetNull`        | `ON DELETE SET NULL`             | Database sets FK to `null`  | EF Core sets FK to `null`  |
| `ClientSetNull`  | None                             | Delete fails                | EF Core sets FK to `null`  |
| `Restrict`       | Same as `NoAction` on SQL Server | Delete fails                | Delete fails               |
| `NoAction`       | `NO ACTION`                      | Delete fails                | Delete fails               |
| `ClientNoAction` | None                             | Delete fails                | EF Core takes no action    |

## When to Use Each Delete Behavior

### Cascade

Use when child entities should automatically be deleted when the parent entity is deleted.

Typical examples:

* Order → OrderLines
* Blog → BlogPosts
* Invoice → InvoiceItems

### ClientCascade

Use when EF Core should delete loaded dependents, but you do not want a database cascade rule.

This is often used when database cascade paths are not allowed or would create conflicts.

### SetNull

Use when child entities should remain in the database after the parent entity is deleted.

Requires a nullable foreign key.

Typical examples:

* Product → Category
* Employee → Manager

### ClientSetNull

Use when EF Core should set foreign keys to `null` for loaded dependents, but you do not want a database `ON DELETE SET NULL` rule.

Requires a nullable foreign key.

### Restrict

Use when related entities must be handled before the parent entity can be deleted.

The delete operation fails until the dependent entities are deleted, moved, or disconnected.

> On SQL Server, `Restrict` behaves the same as `NoAction`.

### NoAction

Use when you want the database to enforce referential integrity and reject invalid deletes.

On SQL Server, the behavior is effectively the same as `Restrict`.

### ClientNoAction

Use when you want full control over dependent entities and do not want EF Core to perform any automatic action.

You are responsible for deleting dependent entities or updating their foreign keys before deleting the parent entity.

This means you must also call `Remove` or `RemoveRange` on the dependent entities when they should be deleted.

## Delete a Category with Related Products

Consider a `Category` that has many related `Product` entities.

```csharp
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;

    public List<Product> Products { get; set; } = new();
}

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }

    public int CategoryId { get; set; }
    public Category Category { get; set; } = null!;
}
```

In this model, `Category` is the principal entity, and `Product` is the dependent entity because it contains the foreign key `CategoryId`.

Now delete a category and include its related products.

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .Include(c => c.Products)
    .SingleAsync(c => c.Name == "Peripherals");

context.Categories.Remove(category);

await context.SaveChangesAsync();
```

If cascade delete is configured (`DeleteBehavior.Cascade` or `DeleteBehavior.CascadeClient`) for this relationship, deleting the category will also delete the related products when the delete operation is saved.

The important point is that `Remove()` does not delete the rows immediately. EF Core tracks the category as `Deleted`, and `SaveChangesAsync()` sends the pending delete operation to the database.

For the basic `Remove()` + `SaveChangesAsync()` workflow, see [Deleting Data](/saving/deleting-data).

## What Is Cascade Delete?

Cascade delete means that deleting a principal entity can also delete the dependent entities that reference it.

For example, if a `Category` is deleted, its related `Product` rows may also be deleted:

```text
Category
  ├── Product: Mechanical Keyboard
  ├── Product: Wireless Mouse
  └── Product: 27-inch Monitor
```

After deleting the category, cascade delete can remove the related products too:

```text
Category deleted
Products deleted
```

This helps avoid invalid foreign key values.

Without cascade delete, the products would still have a `CategoryId` value that points to a category that no longer exists. Most relational databases reject that state because it violates the foreign key constraint.

## Configure DeleteBehavior.Cascade

Cascade delete is configured on a relationship.

You can configure it with the Fluent API by using `OnDelete(DeleteBehavior.Cascade)`.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId)
        .OnDelete(DeleteBehavior.Cascade);
}
```

This configuration tells EF Core to use cascade delete for the `Category` / `Product` relationship.

`DeleteBehavior.Cascade` can delete the related products when the category delete is saved.

If the related products are loaded and tracked by the current `DbContext`, EF Core can apply the cascade behavior before sending the delete commands.

If the related products are not loaded, the database apply the cascade behavior due to the foreign key constraint that was created.

The exact SQL depends on the database provider, the relationship configuration, and whether the dependent entities are tracked.

## Cascade Delete with Required Relationships

In the previous model, `Product.CategoryId` is not nullable.

```csharp
public int CategoryId { get; set; }
```

That makes the relationship required.

A product cannot exist without a category because every product must have a valid `CategoryId`.

For required relationships, cascade delete is often the expected behavior because the dependent entity cannot remain valid without the principal entity.

```csharp
modelBuilder.Entity<Product>()
    .HasOne(p => p.Category)
    .WithMany(c => c.Products)
    .HasForeignKey(p => p.CategoryId)
    .OnDelete(DeleteBehavior.Cascade);
```

In this configuration:

* `Category` is the principal entity
* `Product` is the dependent entity
* `Product.CategoryId` is the foreign key
* deleting a category can delete the related products
* `SaveChangesAsync()` sends the pending delete operation to the database

## Use Restrict or NoAction to Prevent Cascade Delete

Cascade delete is not always the safest choice.

If products should not be deleted automatically when a category is deleted, configure the relationship to prevent cascade delete.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId)
        .OnDelete(DeleteBehavior.Restrict);
}
```

`Restrict` is used here to show the intention clearly: the category should not be deleted while products still depend on it.

You can also use `DeleteBehavior.NoAction` when you want the relationship to avoid automatic cascade delete and let the database constraint enforce referential integrity.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId)
        .OnDelete(DeleteBehavior.NoAction);
}
```

Both `Restrict` and `NoAction` are restrictive behaviors. The exact database behavior can vary depending on the provider.

With this configuration, deleting a category that still has related products can fail because the products still reference the category.

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .Include(c => c.Products)
    .SingleAsync(c => c.Name == "Peripherals");

context.Categories.Remove(category);

await context.SaveChangesAsync();
```

In a relational database, the delete can be rejected if it would leave related products with foreign key values that no longer point to an existing category.

This is useful when related data should be reviewed, moved, or deleted explicitly before deleting the principal entity.

## Use SetNull for Optional Relationships

Cascade delete is not the only possible behavior.

If the dependent entity can exist without the principal entity, the foreign key can be nullable.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }

    public int? CategoryId { get; set; }
    public Category? Category { get; set; }
}
```

In this model, `CategoryId` is nullable.

That means a product can exist without a category.

You can configure the relationship to set the foreign key to `null` when the category is deleted.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId)
        .OnDelete(DeleteBehavior.SetNull);
}
```

`DeleteBehavior.SetNull` can keep the related products in the database and set their `CategoryId` value to `null` when the category delete is saved.

```text
Before delete:

Category: Peripherals
  ├── Product: Mechanical Keyboard  CategoryId = 1
  └── Product: Wireless Mouse       CategoryId = 1

After delete:

Category deleted
Product: Mechanical Keyboard        CategoryId = null
Product: Wireless Mouse             CategoryId = null
```

Use this only when the relationship is optional.

If the foreign key is not nullable, the database cannot set it to `null`.

## How Cascade Delete Works

Cascade delete is part of the normal EF Core saving workflow.

A simplified flow looks like this:

1. You load or attach a principal entity.
2. You call `Remove()` on the principal entity.
3. EF Core tracks the principal entity as `Deleted`.
4. You call `SaveChangesAsync()`.
5. The configured delete behavior can be applied by EF Core for tracked dependents, or by the database through the foreign key constraint.
6. The database receives the required `DELETE` or `UPDATE` commands.
7. The transaction succeeds or fails depending on the relationship configuration and database constraints.

This is the same `Remove()` + `SaveChangesAsync()` workflow used for normal deletes, but the configured relationship behavior decides what happens to the dependents.

With `DeleteBehavior.Cascade`, the dependent products can be marked for deletion.

With `DeleteBehavior.SetNull`, the dependent products can have their `CategoryId` values set to `null`.

With a restrictive behavior such as `Restrict` or `NoAction`, the operation can fail if products still reference the category.

## EF Core Cascade Delete vs Database Cascade Delete

Cascade delete can be applied in two places:

* by EF Core, for entities tracked by the current `DbContext`
* by the database, through the foreign key constraint

When the related products are loaded and tracked, EF Core can apply the configured cascade behavior before saving.

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .Include(c => c.Products)
    .SingleAsync(c => c.Name == "Peripherals");

context.Categories.Remove(category);

await context.SaveChangesAsync();
```

In this case, EF Core knows about the related products because they were loaded with `Include()`.

When the related products are not loaded, the database constraint becomes more important.

```csharp
using var context = new AppDbContext();

var category = await context.Categories
    .SingleAsync(c => c.Name == "Peripherals");

context.Categories.Remove(category);

await context.SaveChangesAsync();
```

In this example, only the category is tracked by the current `DbContext`.

If the database foreign key constraint has cascade delete enabled, the database can delete the related products.

A cascade relationship can create a foreign key constraint similar to this:

```sql
CONSTRAINT [FK_Products_Categories_CategoryId]
FOREIGN KEY ([CategoryId]) REFERENCES [Categories] ([Id])
ON DELETE CASCADE
```

If the database constraint does not cascade and products still reference the category, the delete can fail with a foreign key constraint error.

The exact SQL depends on the database provider, the relationship configuration, and whether the dependent entities are tracked.

## Important Behavior

Cascade delete depends on the relationship configuration.

That means:

* `Remove()` does not delete rows immediately
* `SaveChangesAsync()` is the point where the pending delete operation is sent and the configured behavior can take effect
* cascade delete can apply from the principal entity to dependent entities
* required relationships often use cascade delete because dependents cannot exist without the principal
* `SetNull` requires a nullable foreign key
* restrictive behavior can prevent accidental deletes
* the nullable or non-nullable foreign key influences which delete behaviors are valid
* the database can reject a delete if foreign key constraints would be violated
* EF Core can apply cascade behavior to tracked dependent entities
* the database can apply cascade behavior when the constraint is configured for it

For the basic entity delete workflow, see [Deleting Data](/saving/deleting-data).

For more about how EF Core persists tracked changes, see [SaveChanges](/saving/save-changes).

For more about relationship configuration, see [One-to-Many Relationship Configuration](/configuration/one-to-many-relationship-configuration).

For the official EF Core documentation about relationship delete behavior, see [Cascade Delete](https://learn.microsoft.com/en-us/ef/core/saving/cascade-delete).

## When to Use Cascade Delete

Use cascade delete when dependent data should not exist without the principal entity.

Common examples include:

* deleting order lines when an order is deleted
* deleting product images when a product is deleted
* deleting child records that only exist as part of a parent record
* deleting records that have no independent meaning without the principal entity

In the `Category` / `Product` example, cascade delete may be appropriate if products are not allowed to exist without a category.

```csharp
modelBuilder.Entity<Product>()
    .HasOne(p => p.Category)
    .WithMany(c => c.Products)
    .HasForeignKey(p => p.CategoryId)
    .OnDelete(DeleteBehavior.Cascade);
```

This can keep the database consistent when products should not exist without their category.

## When Not to Use Cascade Delete

Cascade delete is not always appropriate.

Avoid cascade delete when related data should be preserved.

For example, deleting a category should not always delete its products. In many systems, products may need to be moved to another category, archived, hidden, or reviewed before deleting the category.

Use a restrictive behavior when you want the delete to fail until the related data is handled explicitly.

```csharp
modelBuilder.Entity<Product>()
    .HasOne(p => p.Category)
    .WithMany(c => c.Products)
    .HasForeignKey(p => p.CategoryId)
    .OnDelete(DeleteBehavior.Restrict);
```

Use an optional relationship with `SetNull` when the dependent entity can remain valid without the principal.

```csharp
modelBuilder.Entity<Product>()
    .HasOne(p => p.Category)
    .WithMany(c => c.Products)
    .HasForeignKey(p => p.CategoryId)
    .OnDelete(DeleteBehavior.SetNull);
```

Do not use cascade delete just because it makes deletes easier.

Use it only when automatic deletion matches the business rule.

## Common Pitfalls

Be careful with the following mistakes.

### Expecting Related Data to Always Be Deleted

Deleting a principal entity does not always delete related data.

The result depends on the configured relationship behavior and the database constraints.

```csharp
context.Categories.Remove(category);

await context.SaveChangesAsync();
```

Depending on the configuration, this can delete products, set their foreign key to `null`, or fail because products still reference the category.

### Using SetNull with a Required Relationship

`DeleteBehavior.SetNull` requires a nullable foreign key.

This is valid:

```csharp
public int? CategoryId { get; set; }
```

This is not valid for `SetNull`:

```csharp
public int CategoryId { get; set; }
```

If the foreign key column is not nullable, the database cannot set it to `null`.

### Accidentally Deleting Too Much Data

Cascade delete can remove more rows than expected.

For example, deleting one category may delete every product in that category.

```csharp
context.Categories.Remove(category);

await context.SaveChangesAsync();
```

Before using cascade delete, confirm that the dependent data has no independent value.

If the data should be preserved, use a restrictive behavior or move the dependents before deleting the principal.

### Assuming Loaded and Unloaded Dependents Behave the Same

When dependents are loaded and tracked, EF Core can apply the configured behavior in memory before saving.

When dependents are not loaded, the database constraint becomes more important.

```csharp
var category = await context.Categories
    .SingleAsync(c => c.Name == "Peripherals");

context.Categories.Remove(category);

await context.SaveChangesAsync();
```

In this example, the products are not loaded.

If the database is not configured to cascade the delete, the operation can fail.

### Ignoring Foreign Key Constraints

Cascade delete does not remove the need to understand foreign keys.

A dependent row must either:

* be deleted
* have its foreign key set to a valid value
* have its foreign key set to `null`, if the relationship is optional

If none of those options is valid, the database can reject the delete.

## External Resources - Cascade Delete

The following videos are part of the same EF Core relationship series and are useful if you want to see how different delete behaviors work in practical one-to-many scenarios.

Together, they cover cascade delete, no-action behavior, and set-null behavior.

### Video 1 - S1P44: EFCore One to Many Relations Default Cascade Delete

<iframe width="560" height="315" src="https://www.youtube.com/embed/D0T_cDLFfg0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Lars Bilde demonstrates a one-to-many EF Core relationship where deleting a principal entity can also delete related dependent entities. The video is useful for understanding the default cascade delete behavior, how EF Core configures relationships, and how `ON DELETE CASCADE` can appear in the generated database schema.

It fits especially well with the `Category` / `Product` examples in this article, where deleting a principal entity can affect related dependent rows through relationship delete behavior.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=D0T_cDLFfg0&t=0s) — Introduction to EF Core relationships and cascade delete behavior
* [00:30](https://www.youtube.com/watch?v=D0T_cDLFfg0&t=30s) — Practical example of deleting a principal entity and its effect on dependent entities
* [01:30](https://www.youtube.com/watch?v=D0T_cDLFfg0&t=90s) — Why understanding cascade delete behavior is important
* [02:30](https://www.youtube.com/watch?v=D0T_cDLFfg0&t=150s) — Summary of delete behavior configuration and cascade delete warnings

### Video 2 - S1P45: EFCore One to Many Relations Default No Action Delete Behavior

<iframe width="560" height="315" src="https://www.youtube.com/embed/HVK0vNCZiME" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video focuses on a one-to-many EF Core relationship where related entities are not automatically deleted. It is useful for understanding how `NoAction` can prevent automatic cascade delete and why deleting a principal entity can fail when dependent rows still reference it.

Use this resource if you want to understand why restrictive delete behaviors are useful when related data should be reviewed, moved, or deleted explicitly before deleting the principal entity.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=HVK0vNCZiME&t=0s) — Introduction to delete behavior in EF Core
* [00:30](https://www.youtube.com/watch?v=HVK0vNCZiME&t=30s) — Example of configuring `DeleteBehavior.NoAction`
* [01:00](https://www.youtube.com/watch?v=HVK0vNCZiME&t=60s) — Demonstration of how deleting a principal entity affects dependent entities
* [02:00](https://www.youtube.com/watch?v=HVK0vNCZiME&t=120s) — Comparing `Cascade`, `Restrict`, and `NoAction`

### Video 3 - S1P46: EFCore ZeroOrOne to Many Relations Set Null Delete Behavior

<iframe width="560" height="315" src="https://www.youtube.com/embed/zpG-27TJasw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video focuses on `DeleteBehavior.SetNull` in EF Core relationships. It is useful for understanding how dependent rows can remain in the database when a principal entity is deleted, as long as the foreign key is nullable and the relationship is optional.

It complements the `SetNull` section of this article by showing how a relationship can keep dependent entities while clearing the foreign key value.

**Key sections:**

* [00:00](https://www.youtube.com/watch?v=zpG-27TJasw&t=0s) — Introduction to delete behaviors in EF Core
* [01:00](https://www.youtube.com/watch?v=zpG-27TJasw&t=60s) — Explanation of `SetNull` behavior and where it applies
* [02:30](https://www.youtube.com/watch?v=zpG-27TJasw&t=150s) — Practical example of how `SetNull` behaves in code
* [03:30](https://www.youtube.com/watch?v=zpG-27TJasw&t=210s) — Consequences of allowing nullable relationships

## Summary

Cascade delete controls what happens to dependent entities when a related principal entity is deleted.

Key points:

* `Remove()` does not delete rows immediately
* `SaveChangesAsync()` sends the pending delete operation and lets the configured behavior take effect
* `DeleteBehavior.Cascade` can delete related dependents automatically
* `DeleteBehavior.Restrict` or `NoAction` can prevent automatic cascade deletes
* `DeleteBehavior.SetNull` can keep dependents and set the foreign key to `null`
* `SetNull` requires an optional relationship with a nullable foreign key
* EF Core can apply cascade behavior to tracked dependent entities
* the database can apply cascade behavior through foreign key constraints
* deleting a principal entity can fail when dependent rows still reference it

Use cascade delete when dependent data should not exist without the principal entity.

Use restrictive behavior when related data should be reviewed, moved, or deleted explicitly.

Use `SetNull` when dependent data can remain valid without the principal entity.

## Related Articles

If you want to understand how cascade delete fits into the broader EF Core saving and relationship workflow, these pages are the best next steps:

* [Deleting Data](/saving/deleting-data) — how to delete entities with `Remove()` and `RemoveRange()`
* [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
* [ChangeTracker](/saving/change-tracker) — how EF Core tracks entity states before saving
* [One-to-Many Relationship Configuration](/configuration/one-to-many-relationship-configuration) — how to configure one-to-many relationships
* [Referential Constraint Action Options](/relationships/referential-constraint-action-options) — how relationship delete behaviors affect foreign key constraints

## FAQ

### What is cascade delete in EF Core?

Cascade delete means that deleting a principal entity can also delete its dependent entities.

For example, deleting a `Category` can also delete the related `Product` rows if the relationship is configured for cascade delete.

### What happens to related data when I delete an entity?

It depends on the relationship configuration.

Related data can be deleted, have its foreign key set to `null`, or cause the delete operation to fail if database constraints would be violated.

### What is the difference between Cascade, Restrict, NoAction, and SetNull?

`Cascade` can delete dependent entities when the principal entity is deleted.

`Restrict` or `NoAction` can prevent automatic cascade delete and can cause the delete to fail if dependent rows still reference the principal entity.

`SetNull` can set the dependent foreign key to `null`, but only when the relationship is optional and the foreign key is nullable.

### Does EF Core or the database perform cascade delete?

Both are possible.

EF Core can apply cascade behavior to dependent entities that are loaded and tracked by the current `DbContext`.

The database can apply cascade behavior when the foreign key constraint is configured with cascade delete.

### Why does deleting a principal entity fail?

The delete can fail if dependent rows still reference the principal entity and the relationship does not allow cascade delete or setting the foreign key to `null`.

In that case, the database rejects the delete to protect referential integrity.

### When should I avoid cascade delete?

Avoid cascade delete when related data should be preserved, reviewed, moved, archived, or deleted explicitly.

Use cascade delete only when the dependent data should not exist without the principal entity.