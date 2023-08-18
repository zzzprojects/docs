---
title: Table Per Concrete Inheritance
description: EF Core support for inheritance uses the Table Per Concrete pattern
canonical: /inheritance/table-per-concrete
proficiencylevel: Intermediate
status: Published
lastmod: 2023-08-11
---

# EF Core TPC Inheritance

In EF Core 7.0, the table per concrete (also known as TPC) is a mapping strategy where each concrete class in an inheritance hierarchy maps to its table. 

 - In this strategy, each table contains columns for all properties defined in the corresponding class, including the properties inherited from its base class.
 - When using table per concrete (TPC), each concrete class in the inheritance hierarchy will have its own unique set of data in the corresponding table. 
 - This allows for very fine-grained control over the data for each class but can lead to a larger number of tables in the database.

Here's an example of how you can implement a table per concrete pattern in EF Core 7.0 and later:

The following model represents the type of animals such as cats and dogs.

```csharp
public abstract class Animal
{
    public int Id { get; set; }
    public string? Name { get; set; }
}

public class Cat : Animal
{
    public string? MeowSound { get; set; }
}

public class Dog : Animal
{
    public string? BarkSound { get; set; }
}
```

In the TPC mapping pattern, all the concrete types are mapped to individual tables. For TPC mapping call the `modelBuilder.Entity<Animal>().UseTpcMappingStrategy()` on each root entity type as shown below.

```csharp
public class AnimalsDbContext : DbContext
{
    public DbSet<Animal> Animals { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=AnimalsDb;");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTpcMappingStrategy();
        modelBuilder.Entity<Cat>().ToTable("Cats");
        modelBuilder.Entity<Dog>().ToTable("Dogs");
    }
}
```

It will create two tables in the database, one for each concrete class `Cat` and `Dog`, and each table will contain columns for all properties defined in the corresponding class, including the properties inherited from the base class `Animal`. When you create migration or call the `EnsureCreatedmethod`, it will create the following tables.

```csharp
CREATE TABLE [dbo].[Dogs] (
    [Id]        INT            DEFAULT (NEXT VALUE FOR [AnimalSequence]) NOT NULL,
    [Name]      NVARCHAR (MAX) NULL,
    [BarkSound] NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY CLUSTERED ([Id] ASC)
);

CREATE TABLE [dbo].[Cats] (
    [Id]        INT            DEFAULT (NEXT VALUE FOR [AnimalSequence]) NOT NULL,
    [Name]      NVARCHAR (MAX) NULL,
    [MeowSound] NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY CLUSTERED ([Id] ASC)
);
```

There are no tables for the Animal type since this is abstract in the object model. Remember that C# does not allow instances of abstract types. The mapping of properties in base types is repeated for each concrete type. For example, both **Cats** and **Dogs** tables have `Id` and `Name` columns from a base class.

To insert data into the database using the table per concrete mapping strategy, you can use the `Add` method on the `DbSet` for each concrete class to add new entities to the context, and then call the `SaveChanges` method to save the changes to the database.

Here's an example:

```csharp
using (var context = new AnimalsDbContext())
{
    var cat = new Cat
    {
        Name = "Fluffy",
        MeowSound = "Meow"
    };

    context.Animals.Add(cat);

    var dog = new Dog
    {
        Name = "Buddy",
        BarkSound = "Woof"
    };

    context.Animals.Add(dog);

    context.SaveChanges();
}
```

In this example, two new entities, one for `Cat` and one for `Dog`, are added to the context and then saved to the database. The Entity Framework Core runtime will use the table per concrete mapping strategy to determine which table to insert the data into.

The following example retrieves data from the database using the table per concrete mapping strategy in Entity Framework Core.

```csharp
using (var context = new MyContext())
{
    var cats = context.Animals
        .OfType<Cat>()
        .ToList();

    foreach (var cat in cats)
    {
        Console.WriteLine($"Cat: {cat.Name}, Sound: {cat.MeowSound}");
    }

    var dogs = context.Animals
        .OfType<Dog>()
        .ToList();

    foreach (var dog in dogs)
    {
        Console.WriteLine($"Dog: {dog.Name}, Sound: {dog.BarkSound}");
    }
}
```

In the example, the `OfType` method is used to filter the entities retrieved from the `DbSet` based on their concrete type, either `Cat` or `Dog`. The filtered entities are then loaded into memory using the `ToList` method and displayed in the console.

The Entity Framework Core runtime will use the table per concrete mapping strategy to determine which table to retrieve the data from.

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="when-did-tpc-inheritance-receive-support-in-ef-core" itemprop="name">When did TPC inheritance receive support in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

EF Core support TPC inheritance starting from EF Core 7.0

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-map-entities-in-the-onmodelcreating-method-to-impelement-tpc-inheritance-in-ef-core" itemprop="name">How to map entities in the 'OnModelCreating' method to implement TPC inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

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

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="is-there-any-performance-issue-related-to-tpc-inheritance-in-ef-core" itemprop="name">Is there any performance issue related to TPC inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

The TPC inheritance could become slow if you have too many concrete types.

If you query the root entity, a SQL will be created with a `UNION ALL` join that will include all tables from your concrete class.

```sql
SELECT [ColumnList], 'DiscriminatorName1' AS [Discriminator]
FROM [TableName1]
WHERE [predicate]
UNION ALL
SELECT [ColumnList], 'DiscriminatorName2' AS [Discriminator]
FROM [TableName2]
WHERE [predicate]
UNION ALL
SELECT [ColumnList], 'DiscriminatorName3' AS [Discriminator]
FROM [TableName3]
WHERE [predicate]
UNION ALL
SELECT [ColumnList], 'DiscriminatorName4' AS [Discriminator]
FROM [TableName4]
WHERE [predicate]
```

There is no problem if you have only 2 or 3 tables, but if you implement a strategy with many concrete classes, you might end up with some performance issues when querying.

That is one of the reasons why this is recommended to only have 2 or 3 concrete classes in the TPC strategy.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="can-i-have-multiple-dbset-instance-for-each-class-within-a-tpc-inheritance-in-ef-core" itemprop="name">Can I have multiple DbSet instances for each class within a TPC inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Yes, you can have multiple `DbSet` instances, and it's often recommended based on how you intend to query your entities.
Here is an example:

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

In this example, we have mapped the root entity `Animal` even if it is an abstract class. When you query the `DbSet<Animal>`, the result will be a list of `Animal` objects, each of which could be an instance of either `Cat` or `Dog`.

Furthermore, you can query the Cats or Dogs tables directly using their corresponding `DbSet`.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="can-i-combine-tpc-inheritance-with-another-inheritance-in-ef-core" itemprop="name">Can I combine TPC inheritance with another inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

No, you cannot combine the TPC inheritance with another inheritance.

</div></div>
</details>

</div>

