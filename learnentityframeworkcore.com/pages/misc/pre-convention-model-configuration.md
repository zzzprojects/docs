---
title: Pre-convention Model Configuration in Entity Framework Core
description: EF Core 6.0 allows this mapping configuration to be specified once for a given type. It will then be applied to all properties of that type in the model.
canonical: /misc/pre-convention-model-configuration
status: Published
lastmod: 2025-07-13
---

# EF Core Pre-convention Model Configuration

In EF Core, pre-convention model configuration refers to the configuration of entities and properties before the model is created by EF Core. This can be done using various methods, including data annotations, fluent API, and the `OnModelCreating` method in the `DbContext` class.

In previous versions of EF Core, explicit mapping was required for each property when it differed from the default, including details such as the maximum length of strings and decimal precision, as well as value conversion for the property type. This meant that either:

 - Configuration of the model builder for each property was necessary
 - A mapping attribute was placed on each property, or
 - Explicit iteration over all properties of all entity types and utilization of the low-level metadata APIs was performed during model building, which was error-prone and challenging to do accurately as the list of entity types and mapped properties could be subject to change.

EF Core 6.0 simplifies this process by allowing the mapping configuration to be specified once for a given type and automatically applied to all properties of that type in the model. This is referred to as a pre-convention model configuration as it sets up elements of the model that will then be used by the model building conventions.

Here's an example of a pre-convention model configuration: we have two entities in our model, i.e., `Author` and `Book`.

```csharp
public class Author
{
    public int Id { get; set; }
    public string? FirstName { get; set; }
    public string? LastName { get; set; }
    public ICollection<Book> Orders { get; } = new List<Book>();
}

public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public DateTime PublishDate { get; set; }
    public bool IsAvailable { get; set; }
    public decimal Price { get; set; }

    public Author Author { get; set; }
}
```

By using a pre-convention model configuration, you can specify how entities and properties should be mapped to the database, including the column names, data types, and constraints. This makes it easier to control and maintain the schema of the database and ensures that it is in sync with your domain model.

This configuration is achieved by overriding the `ConfigureConventions` method in your `DbContext`.

```csharp
public class BookStoreContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    public DbSet<Book> Books { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=BookStoreDb;");
    }
    protected override void ConfigureConventions(ModelConfigurationBuilder configurationBuilder)
    {
        configurationBuilder
            .Properties<string>()
            .AreUnicode(false)
            .HaveMaxLength(1024);

        configurationBuilder
            .Properties<DateTime>()
            .HaveConversion<long>();

        configurationBuilder
            .Properties<bool>()
            .HaveConversion<BoolToZeroOneConverter<int>>();

        configurationBuilder
            .Properties<decimal>()
            .HavePrecision(10,3);
    }

}
```

When you run the migrations or call the `EnsureCreated` method, it will create the following tables with pre-convention model configuration.

```csharp
CREATE TABLE [dbo].[Authors] (
    [Id]        INT            IDENTITY (1, 1) NOT NULL,
    [FirstName] VARCHAR (1024) NULL,
    [LastName]  VARCHAR (1024) NULL,
    CONSTRAINT [PK_Authors] PRIMARY KEY CLUSTERED ([Id] ASC)
);

CREATE TABLE [dbo].[Books] (
    [Id]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       VARCHAR (1024)  NOT NULL,
    [PublishDate] BIGINT          NOT NULL,
    [IsAvailable] INT             NOT NULL,
    [Price]       DECIMAL (10, 3) NOT NULL,
    [AuthorId]    INT             NOT NULL,
    CONSTRAINT [PK_Books] PRIMARY KEY CLUSTERED ([Id] ASC),
    CONSTRAINT [FK_Books_Authors_AuthorId] FOREIGN KEY ([AuthorId]) REFERENCES [dbo].[Authors] ([Id]) ON DELETE CASCADE
);


GO
CREATE NONCLUSTERED INDEX [IX_Books_AuthorId]
    ON [dbo].[Books]([AuthorId] ASC);
```

