---
title: Table Per Type Inheritance
description: EF Core support for inheritance uses the Table Per Type pattern
canonical: /inheritance/table-per-type
proficiencylevel: Intermediate
status: Published
lastmod: 2025-07-13
---

# EF Core TPT

Table-per-type (TPT) is a mapping strategy used in the object-relational mapping (ORM) to represent inheritance in a relational database. In TPT, each subtype of an inheritance hierarchy is represented as its table in the database, and the base type is represented as a separate table. 

 - Each subtype table contains columns that correspond to the properties of that subtype, as well as a foreign key to the base type table that identifies the corresponding row in the base type table for each subtype row.
 - TPT is a useful mapping strategy when subtypes have very different sets of properties, as it allows the columns in each table to correspond exactly to the properties of each subtype. 
 - However, it can result in a lot of tables in the database, and can make it more difficult to perform queries that involve multiple subtypes. 

To use TPT in EF Core, you need to define an inheritance hierarchy among your entities as shown below.

```csharp
public class Animal
{
    public int Id { get; set; }
    public string? Name { get; set; }
}

public class Mammal : Animal
{
    public string? Type { get; set; }
}

public class Bird : Animal
{
    public int Wingspan { get; set; }
}
```

In this example, the `Animal` base class is defined with `Id` and `Name` properties, and two subtypes, `Mammal` and `Bird` are defined with additional properties.

In the `OnModelCreating` method of the `AnimalContext`, instead of calling `ToTable` on each entity type, just call `modelBuilder.Entity<Animal>().UseTptMappingStrategy()` on each root entity type; this will generate the table names by convention. 

```csharp
public class AnimalContext : DbContext
{
    public DbSet<Animal> Animals { get; set; }
    public DbSet<Bird> Birds { get; set; }
    public DbSet<Mammal> Mammals { get; set; }
	
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTptMappingStrategy();
    }

}
```

To insert data into a database using the Table-per-type (TPT) mapping strategy in Entity Framework Core (EF Core), you can create instances of the entities you want to insert, and then add them to the appropriate `DbSet` and call the `SaveChanges` method on the `DbContext` to persist the changes to the database.

Here's an example of how you might insert data into a database using TPT mapping in EF Core:

```csharp
using (var context = new AnimalContext())
{
    var mammal = new Mammal
    {
        Name = "Lion",
        Type = "Carnivore"
    };
    context.Mammals.Add(mammal);
	
    var bird = new Bird
    {
        Name = "Eagle",
        Wingspan = 6
    };
	
    context.Birds.Add(bird);
	
    context.SaveChanges();
}
```

In this example, we create an instance of the `AnimalContext`, which is our `DbContext`, and then create two instances of the `Mammal` and `Bird` entities that we want to insert into the database. 

 - We then use the `Add` method on the `Mammals` and `Birds` properties of the `AnimalContext` to add the entities to the appropriate `DbSet`, and finally call the `SaveChanges` method to persist the changes to the database. 
 - The TPT mapping strategy will ensure that the entities are inserted into the correct table in the database.

To retrieve data from a database using the Table-per-type (TPT) mapping strategy in Entity Framework Core (EF Core), you can use LINQ to query the appropriate `DbSet` for the entities you want to retrieve.

Here's an example of how you might retrieve data from a database using TPT mapping in EF Core:

```csharp
using (var context = new AnimalContext())
{
    var mammals = context.Animals
        .OfType<Mammal>()
        .ToList();
	
    foreach (var mammal in mammals)
    {
        Console.WriteLine($"{mammal.Name} ({mammal.Type})");
    }
	
    var birds = context.Animals
        .OfType<Bird>()
        .ToList();
	
    foreach (var bird in birds)
    {
        Console.WriteLine($"{bird.Name} ({bird.Wingspan}m)");
    }
}
```

In this example, we create an instance of the `AnimalContext`, which is our `DbContext`, and then use the `OfType` method to retrieve the `Mammal` and `Bird` entities from the Animals `DbSet`. 

 - The `OfType` method allows us to retrieve only the entities of a specific type from a `DbSet`. 
 - We then use the `ToList` method to materialize the entities into a list and finally use a foreach loop to iterate over the list and print out the properties of each entity. 
 - The TPT mapping strategy will ensure that the entities are retrieved from the correct table in the database.

Overall, the choice of whether to use TPT or another mapping strategy, such as table-per-hierarchy (TPH) or table-per-concrete-class (TPC), depends on the specific requirements of your application and the needs of your data.