---
title: Entity Framework Core One To One Relationships Conventions
description: How to design your model so that it maps to a one-to-one relationship by convention in Entity Framework Core
canonical: /conventions/one-to-one-relationship
status: Published
lastmod: 2023-02-23
---

# EF Core One to One

Entity Framework Core will create a one-to-one relationship when both entities involved in the relationship contain a navigation property to the other, and the dependent entity includes a foreign key property for the principal entity. The following example illustrates a one-to-one relationship between `Author` and `AuthorBiography`:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public AuthorBiography Biography { get; set; }
}

public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public string Biography { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string PlaceOfBirth { get; set; }
    public string Nationality { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```
EF Core will generate a unique index on the `AuthorId` foreign key column in the AuthorBiographies table to ensure that only one biography can be associated with any particular author. 

#### Further Reading

[Configuring one to one relationships](/configuration/one-to-one-relationship-configuration)

