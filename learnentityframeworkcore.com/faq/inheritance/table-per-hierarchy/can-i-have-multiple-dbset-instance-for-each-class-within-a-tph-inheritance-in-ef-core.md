---
id: 4936d217-b37d-49fe-b66c-11f22238671d
position: 6
title: Can I have multiple DbSet instances for each class within a TPH inheritance in EF Core?
---

Yes, every concrete and abstract type can be in its own `DbSet`. Here is an example:

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

In this example, we have mapped the abstract `Animal` type to the `DbSet<Animal>` and both concrete types to `DbSet<Cat>` and `DbSet<Dog>`.

When you query the `DbSet<Animal>`, the result will be a list of `Animal`, which will be either of `Cat` or `Dog` type.