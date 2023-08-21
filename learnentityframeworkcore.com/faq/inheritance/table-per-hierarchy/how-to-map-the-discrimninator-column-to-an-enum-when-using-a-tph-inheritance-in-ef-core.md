---
id: 5bbd08f9-8da9-47ec-b4d4-1d0f7591f359
position: 2
title: How to map the discriminator column to an enum when using a TPH inheritance in EF Core?
---

You can map a discriminator column to an enum type by first creating the enum and then mapping the `TPH` inheritance by using the enum type as a generic parameter to the `HasDiscriminator` method. 

Here is an example:

```csharp
public enum AnimalType
{
    Cat,
    Dog
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Animal>()
		.HasDiscriminator<AnimalType>("DiscriminatorColumnName")
		.HasValue<Cat>("Cat")
		.HasValue<Dog>("Dog");
}
```