---
id: b3e0c1c3-86dd-43dc-ae69-80e8a06ca564
position: 3
title: How to change the name of the discriminator column when using a TPH inheritance in EF Core?
---

You can choose the discriminator column name in the `OnModelCreating` by calling the `HasDiscriminator<string>([DiscriminatorColumnName])` method when mapping your entity type.

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