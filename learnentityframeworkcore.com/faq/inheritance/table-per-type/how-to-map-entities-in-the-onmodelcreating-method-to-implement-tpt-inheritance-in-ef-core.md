---
id: e245c950-d0b7-4981-a075-6a61d1b02b4b
position: 2
title: How to map entities in the 'OnModelCreating' method to implement TPT inheritance in EF Core?
---

To implement the TPT inheritance strategy in EF Core, you need to:

1. Call the `UseTptMappingStrategy` method for your root entity (optional if you already specify a table name for every entity but highly recommended to make your intention clear)
2. Call the `ToTable([TableName])` method for every concrete entity if you want a different table name by default

Here is an example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	// optional if you already specify a table name for every entity but highly recommended to make your intention clear
	modelBuilder.Entity<Animal>().UseTptMappingStrategy();
	
	modelBuilder.Entity<Animal>().ToTable("Animal");
	modelBuilder.Entity<Cat>().ToTable("Cat");
	modelBuilder.Entity<Dog>().ToTable("Dog");
}
```

In this example, even if `Animal` is an abstract class, it must be mapped to its table as it will contain data such as the key for both `Cat` and `Dog` classes.