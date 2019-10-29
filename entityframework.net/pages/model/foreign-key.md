---
PermaID: 1000123
Name: Foreign Key
---

# ForeignKey

The `ForeignKey` attribute is used to specify which property is the foreign key in a relationship. It is used to express the relationship between two tables.

 -  The default code first convention for ForeignKey relationship expects foreign key property name match with the primary key property.
 -  If your code is not using the default code first conventions, then you can use `ForeignKey` attribute, so that code first can identify that it contains the foreign key.

In the following example, the `AuthorBiographyId` property in the `AuthorBiography` entity does not follow the Entity Framework convention for foreign key names, and it will create an `AuthorId` field which it will configure as a foreign key.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
}

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

The `ForeignKey` attribute can be used to configure the relationship correctly, and prevent the generation of an `AuthorId` column in the `AuthorBiography` table.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
}

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
[Try it](https://dotnetfiddle.net/BfckEW)