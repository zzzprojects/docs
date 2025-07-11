---
id: ff447601-3e9f-41bc-97c5-e316fdd497d5
position: 4
title: Can I have multiple DbSet instances for each class within a TPT inheritance in EF Core?
---

Sure, it's always recommended to have multiple `DbSet` instances when working with any inheritance such as `TPT`. It makes it easier to query your entities when you already know exactly the type.

Here is an example:

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

In this example, 

- **The `DbSet<Animal> Animals`**: Allows you to query any type that inherits from `Animal` such as `Cat` and `Dog`.
- **The `DbSet<Cat> Cats`**: Allows you to directly query `Cat`.
- **The `DbSet<Dog> Dogs`**: Allows you to directly query `Dog`.