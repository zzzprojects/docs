---
PermaID: 1000100
Name: Fluent API
---

# Fluent API

When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF. 

 - Fluent API specify the model configuration that you can with data annotations as well as some additional functionality that can not be possible with data annotations. 
 - We can configure many different things by using it because it provides more configuration options than data annotation attributes.
 - Data annotations and the fluent API can be used together, but precedence of **Fluent API > data annotations > default conventions**.

## DbModelBuilder

DbModelBuilder is typically used to configure a model by overriding DbContext.OnModelCreating(DbModelBuilder) .

You can override the `DbContext.OnModelCreating` method and use a parameter modelBuilder of type `DbModelBuilder` to configure domain classes.

##  Key

You can use the **HasKey()** method to configure the name of the primary key constraint in the database.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasKey(a => a.AuthId);
}
```
[Try it](https://dotnetfiddle.net/x4nMB1)

## Concurrency Token (ConcurrencyCheck)

You can use the **IsConcurrencyToken()** method to configure a property as a concurrency token.


```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .Property(a => a.LastName)
        .IsConcurrencyToken();
}
```
[Try it](https://dotnetfiddle.net/OwMcdJ)

## Exclude Types or Properties

You can use the **Ignore()** method to exclude a type or a property from the model.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .Ignore(a => a.IgnoreProperty);
}
``` 
[Try it](https://dotnetfiddle.net/wzep6l)

## Required

You can use the **IsRequired()** method to indicate that a property is required.

```csharp   
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .Property(a => a.LastName)
        .IsRequired();

    modelBuilder.Entity<Author>()
        .Property(a => a.FirstName)
        .IsRequired();
}
``` 
[Try it](https://dotnetfiddle.net/XHhJWj)

## Table Mapping

You can use the **ToTable()** method to configure the table that a type maps to.

```csharp   
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
     modelBuilder.Entity<Author>()
        .ToTable("AuthorsData");
}
``` 
[Try it](https://dotnetfiddle.net/KeP2EJ)

## Column Mapping

You can use the **HasColumnName()** method to configure the column to which a property is mapped.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .Property(a => a.AuthorId)
        .HasColumnName("Author_Id");
}
```  
[Try it](https://dotnetfiddle.net/d683kR)

## Foreign Key

You can use the **HasForeignKey()** method to configure the foreign key constraint name for a relationship.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasRequired<Author>(b => b.Author)
        .WithMany(a => a.Books)
        .HasForeignKey<int>(b => b.AuthorId);
}
``` 
[Try it](https://dotnetfiddle.net/GrB0j8)

## Default Schema

You can use the **HasDefaultSchema()** method to specify a default schema.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.HasDefaultSchema("Admin");
}
``` 
[Try it](https://dotnetfiddle.net/q83NlG)

## Index

You can use the **HasColumnAnnotation()** method to configure the name of an index.


```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .Property(a => a.Email)
        .HasColumnType("VARCHAR")
        .HasMaxLength(450)
        .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute("Index_Email") { IsUnique = true }));
}
``` 

[Try it](https://dotnetfiddle.net/2vLIfu)