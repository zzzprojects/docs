---
PermaID: 1000054
Name: One to One
---

# One-to-One Relationships

In a one-to-one relationship, each row of data in one table is linked to zero or one row in the second table. For example, the `Author` and `AuthorBiography` have a one-to-one relationship.

 - This type of relationship is not common because most information related in this way would be an all-in-one table.
 - When you define a one-to-one relationship in your model, you use a reference navigation property in each class.

Let's say we have an `Author` class which contains author name and collection of books.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
}
```

Now we want to add another class `AuthorBiography` class to contain some more information about the author like `Biography`, `DateOfBirth` and `Nationality`, etc.

```csharp
public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public string Biography { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string PlaceOfBirth { get; set; }
    public string Nationality { get; set; }
    public virtual Author Author { get; set; }
}
```

## Configure One-to-One Relationship

Let's also add a `Biography` property to the 'Author' class, so that we can navigate both directions.

```csharp
public class Author
{
    [Key]
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public virtual AuthorBiography Biography { get; set; }
}
```

Code First can't determine which class is dependent and when you execute to build the model, an exception is thrown.

> System.InvalidOperationException: 'Unable to determine the principal end of an association between the types 'EFDemo.Author' and 'EFDemo.AuthorBiography'. The principal end of this association must be explicitly configured using either the relationship fluent API or data annotations.'

[Try it](https://dotnetfiddle.net/rjUmxe)

### Using Data Annotation

You can solve this problem very easily by using a `ForeignKey` data annotation on the dependent class so that code first can identify that it contains the foreign key.

```csharp
public class AuthorBiography
{
    [ForeignKey("Author")]
    public int AuthorBiographyId { get; set; }
    public string Biography { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string PlaceOfBirth { get; set; }
    public string Nationality { get; set; }
    public virtual Author Author { get; set; }
}
```

[Try it](https://dotnetfiddle.net/LISWtb)

In this case, the `AuthorBiography` is depending on `Author`, so the `AuthorBiographyId` property is a primary as well as a foreign key.

### Using Fluent API

You can also use Fluent API to configure a one-to-one relationship between the `Author` and `AuthorBiography` entities.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasOptional(a => a.Biography)
        .WithRequired(ab => ab.Author);
}
```

[Try it](https://dotnetfiddle.net/E6BpOz)

 - The `HasOptional()` method marks the `Biography` property optional in `Author` entity.
 - The `.WithRequired()`method marks `Author` property as required in `AuthorBiography` entity.
 - it will throw an exception when the AuthorBiography entity is saved without the `Author` navigation property.