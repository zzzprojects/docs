---
title: Applying Seed Data To The Database
description: Seeding data in Entity Framework Core Migrations
canonical: /migrations/seeding
status: Published
lastmod: 2025-07-13
---

# EF Core Seed Data

Applying seed data to a database refers to the process of inserting initial data into a database, usually when the database is first created. This data serves as a baseline and can be used for testing, and development, and to provide some context for the application that will be built on top of the database.

## OnModelCreating


EF Core has a formal API for applying seed data to the database as part of your migration - the `HasData` method of the `EntityTypeBuilder<T>` method, which is exposed by the `ModelBuilder.Entity<T>` method, accessible in the `OnModelCreating` method of the `DbContext` class. So, seeding forms part of the model configuration.

Here's an example of how you might use EF Core to apply seed data to an SQLite database:

```csharp
public class ApplicationDbContext : DbContext
{
    public DbSet<Author> Authors { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>().HasData(
            new Author
            {
                AuthorId = 1,
                FirstName = "William",
                LastName = "Shakespeare"
            }
        );
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=AuthorsData.db");
    }
}
```

In this example, the `OnModelCreating` method is overridden to seed the `Authors` table with one row of data. The `OnConfiguring` method is overridden to specify the database to use (an SQLite database with the file name **AuthorsData.db**).

One way to seed data in EF Core is by using the `DbContext.Database.EnsureCreated()` method in your `DbContext` class. This method will create the database and apply any seed data that you have defined in your `DbContext` class.

```csharp
using (var context = new ApplicationDbContext())
{
    context.Database.EnsureCreated();
}
```

This will create the database and apply the seed data defined in the `OnModelCreating` method. You can run this code when your application starts or as part of a setup script.

## Seeding Related Data

You can seed related data in the database by creating multiple entities in the `OnModelCreating` method and defining relationships between them.

Related data must be added separately via the `HasData` method applied to the related entity's `EntityTypeBuilder`. Here is how you can add an author together with related books:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>().HasData(
        new Author
        {
            AuthorId = 1,
            FirstName = "William",
            LastName = "Shakespeare"
        }
    );

    modelBuilder.Entity<Book>().HasData(
        new Book { BookId = 1, AuthorId = 1, Title = "Hamlet" },
        new Book { BookId = 2, AuthorId = 1, Title = "King Lear" },
        new Book { BookId = 3, AuthorId = 1, Title = "Othello" }
    );
}
```
Note that the Identity value for each book is provided and that the Foreign Key value has been set to that of the seed Author.

If you prefer to keep your `OnModelCreating` method clean, you can refactor these operations into an extension method on the `ModelBuilder` type:

```csharp
public static class ModelBuilderExtensions
{
    public static void Seed(this ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>().HasData(
            new Author
            {
                AuthorId = 1,
                FirstName = "William",
                LastName = "Shakespeare"
            }
        );
        modelBuilder.Entity<Book>().HasData(
            new Book { BookId = 1, AuthorId = 1, Title = "Hamlet" },
            new Book { BookId = 2, AuthorId = 1, Title = "King Lear" },
            new Book { BookId = 3, AuthorId = 1, Title = "Othello" }
        );
    }
}
```

Then you can call the `Seed` method in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Seed();
}
```

## Amending Seed Data

You can add or alter seed data during subsequent migrations. Entity Framework Core will compare the data in the database and generate the appropriate `insert`, `update`, and `delete` methods.

## Before Entity Framework Core 2.1

Previous to version 2.1, the advice was to create code for adding the seed data and then to call that code along with other application startup code in `Program.Main()`. 

The following code illustrates this pattern, starting with a method that seeds the database with countries:

```csharp
public class DataSeeder
{
    public static void SeedCountries(DbContext context)
    {
        if (!context.Countries.Any())
        {
            var countries = new List<Country>
            {
                new Country { Name = "Afghanistan" },
                new Country { Name = "Albania" },
                new Country { Name = "Algeria" },
                new Country { Name = "Andorra" },
                new Country { Name = "Angola" },
                new Country { Name = "Antigua and Barbuda" },
                new Country { Name = "Argentina" },
                new Country { Name = "Armenia" },
                new Country { Name = "Aruba" },
                new Country { Name = "Australia" },
                new Country { Name = "Austria" },
                new Country { Name = "Azerbaijan" },
                ...
            };
            context.AddRange(countries);
            context.SaveChanges();
        }
    }
}
```
While this example uses hardcoded country names (and is curtailed for brevity), the code could just as simply read seed data from an external location such as a web service, text, or XML file.

The `SeedCountries` method is called in the `Main` method of the `Program` class. This example illustrates how this might be achieved in an ASP.NET application:

```csharp
public void Main(string[] args)
{
    var host = BuildWebHost(args);
    using (var scope = host.Services.CreateScope())
    {
        var services = scope.ServiceProvider;
        var context = serviceScope.ServiceProvider.GetService<ApplicationDbContext>();
        DataSeeder.SeedCountries(context);
    }
    host.Run();
}
```

You could also refactor the code that calls the seed method into an extension method on `IWebHost`:

```csharp
public static IWebHost SeedData(this IWebHost host)
{
    using (var scope = host.Services.CreateScope())
    {
        var services = scope.ServiceProvider;
        var context = serviceScope.ServiceProvider.GetService<ApplicationDbContext>();
        
        SeedCountries(context);
    }
    return host;
}
```

Then you can chain this method in the `Program.Main` method:

```csharp
public void Main(string[] args)
{
    BuildWebHost(args).SeedData().Run();
}
```
