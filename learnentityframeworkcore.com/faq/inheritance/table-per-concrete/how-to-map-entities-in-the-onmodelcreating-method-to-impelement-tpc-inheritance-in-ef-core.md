---
id: d6352a24-36f4-4bce-9dac-28c5ad48295c
position: 2
title: How to map entities in the 'OnModelCreating' method to implement TPC inheritance in EF Core?
---

To implement the TPC inheritance strategy in EF Core, you need to:

1. Call the `UseTpcMappingStrategy` method for your root entity
2. Call the `ToTable([TableName])` method for every concrete entity

Here is an example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Animal>().UseTpcMappingStrategy();
	modelBuilder.Entity<Cat>().ToTable("Cats");
	modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

In this example, the `Animal` is an abstract class, meaning you don't map it to a table. However, if the `Animal` is a concrete class, you must also map it to a table like other concrete classes.
