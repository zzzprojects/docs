---
title: The ForeignKey Attribute
description: Usage of the ForeignKey Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/foreignkey-attribute
status: Published
lastmod: 2025-07-11
---

# EF Core ForeignKey Attribute

The `ForeignKey` attribute is used to specify which property is the foreign key in a relationship. 

In the following example, the `AuthorFK` property in the `Book` entity does not follow [Entity Framework Core's convention for foreign key names](/conventions#foreign-key). Left as it is, Entity Framework Core will create an `AuthorFK` field *and* an `AuthorId` field which it will configure as a foreign key:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
    public int AuthorFK { get; set; }
}
```
The `ForeignKey` attribute can be used in the following ways to configure the relationship correctly, preventing the generation of an `AuthorId` column in the Books table.

1. On the foreign key property in the dependent class, passing in the name of the navigation property:
    ```csharp
    public class Book
    {
        public int BookId { get; set; }
        public string Title { get; set; }
        public Author Author { get; set; }
        [ForeignKey("Author")]
        public int AuthorFK { get; set; }
    }
    ```
2. On the navigation property in the dependent class, passing in the name of the foreign key property:

    ```csharp
    public class Book
    {
        public int BookId { get; set; }
        public string Title { get; set; }
        [ForeignKey("AuthorFK")]
        public Author Author { get; set; }
        public int AuthorFK { get; set; }
    }
    ```
3. On the navigation property in the principal class, passing in the name of the foreign key property in the dependent class:
    ```csharp
    public class Author
    {
        public int AuthorId { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        [ForeignKey("AuthorFK")]
        public ICollection<Book> Books { get; set; }
    }
    ```
If you place the `ForeignKey` attribute on a navigation property that has multiple foreign keys, separate each one with a comma.

#### Fluent API

The [HasForeignKey method](/configuration/fluent-api/hasforeignkey-method) is used to specify foreign keys when using the Fluent API.
