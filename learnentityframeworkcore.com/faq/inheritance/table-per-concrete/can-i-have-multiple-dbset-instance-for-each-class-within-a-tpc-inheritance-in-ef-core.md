---
id: 4d5a3a50-89c4-40a2-8368-c1935ddc6f1f
position: 4
title: Can I have multiple DbSet instances for each class within a TPC inheritance in EF Core?
---

Yes, you can have multiple `DbSet` instances, and it's often recommended based on how you intend to query your entities.

Here is an example:

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

In this example, we have mapped the root entity `Animal` even if it is an abstract class. When you query the `DbSet<Animal>`, the result will be a list of `Animal` objects, each of which could be an instance of either `Cat` or `Dog`.

Furthermore, you can query the Cats or Dogs tables directly using their corresponding `DbSet`.