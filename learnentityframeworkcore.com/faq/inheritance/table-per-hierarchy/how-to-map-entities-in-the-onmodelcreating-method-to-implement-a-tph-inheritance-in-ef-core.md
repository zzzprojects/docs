---
id: 0bea9cc2-6645-4888-b2e4-085300d459db
position: 4
title: How to map entities in the 'OnModelCreating' method to implement a TPH inheritance in EF Core?
---

To implement the TPH inheritance strategy in EF Core, you need to:

1. Call the `HasDiscriminator<type>([DiscriminatorName])` method for your root entity
2. Call the `HasValue([DiscriminatorName])` method for every concrete entity

Here is an example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Animal>()
		.HasDiscriminator<string>("DiscriminatorColumnName")
		.HasValue<Cat>("Cat")
		.HasValue<Dog>("Dog");
}
```

In this configuration:

- The `Animal` is an abstract class, meaning you don't map it to a table. 
- The `.HasDiscriminator<string>("DiscriminatorColumnName")` will create a column name named `DiscriminatorColumnName` of type `string`.
- The `HasValue<Cat>("Cat")` map the `Cat` type to the discriminator value `Cat`.
- The `HasValue<Dog>("Dog")` map the `Dog` type to the discriminator value `Dog`.