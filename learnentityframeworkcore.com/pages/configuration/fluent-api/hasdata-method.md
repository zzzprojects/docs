---
title: The Fluent API HasData Method
description: Usage of the Fluent API HasData Method in Entity Framework Core
canonical: /configuration/fluent-api/hasdata-method
status: Published
lastmod: 2025-07-11
---

# EF Core HasData


The Entity Framework Core Fluent API `HasData` method is designed to help provide [seed data for migrations](/migrations/seeding) to the specified entity. It can also be used to seed a database with data outside of a migration. This can be useful for testing purposes, when a database should start in a known state.

The following example illustrates seeding by adding an entry to the Authors table:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>().HasData(
        new Author
        {
            AuthorId = 1,
            FirstName = "William",
            LastName = "Shakespeare"
        }
    );
}
```

The key value is required so it must be supplied. When the `HasData` method is used, EF Core will automatically generate `SET IDENTITY INSERT ON` for the relevant table, and then set it to `OFF` once the seeding has completed.

If you want to use the HasData method outside of a migration, you must call the  `Database.EnsureCreated()` method, which you would normally do as part of a test, or in database initialisation code. 

### Data Annotations

There is no Data Annotation equivalent to the `HasData` method.
