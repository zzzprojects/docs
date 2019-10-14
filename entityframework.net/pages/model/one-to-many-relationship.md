---
PermaID: 1000055
Name: One to Many
---

# One-to-Many Relationships

In a one-to-many relationship, each row of data in one table is linked to one or more rows in the second table. It is the most common type of relationship.

 - A one-to-many relationship happens when the primary key of one table becomes foreign keys in another table.
 - The foreign key is defined in the table that represents the many end of the relationship.

Let's say we have `Author` and `Book` entities.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```

## Configure One-to-Many Relationship

In EF6, most of the times you don't need to configure the one-to-many relationship because one-to-many relationship conventions cover all combinations. You can establish a one-to-many relationship by using any of the following code first conventions.

### Code First Conventions

#### Add Reference Navigation Property

Include a reference navigation property of type `Author` in the `Book` entity class.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

Adding an `Author` navigation property will create a one-to-many relationship between the `Authors` and `Books` tables in the database by adding a foreign key `Author_AuthorId` to `Books` table.

#### Add Collection Navigation Property

You can achieve one-to-many relationship by adding the collection navigation property of `Book` entity in the `Author` entity class.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```

[Try it](https://dotnetfiddle.net/MPx1yh)

#### Add Navigation Properties in Both Entities

Adding navigation properties at both entities will also result in a one-to-many relationship. For example, `Author` class contains a collection of `Books` while `Book` class contains a navigation property of type `Author`.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
[Try it](https://dotnetfiddle.net/DrES9C)

#### Fully Defined Relationship

A fully defined relationship at both ends will also create a one-to-many relationship. For example, the `Book` entity includes foreign key property `AuthorId` with its reference property `Author` and `Author` class contains a collection of `Books`.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```

[Try it](https://dotnetfiddle.net/moGbUs)

All these conventions produces the same result in the database.

### Using Fluent API

You can also configure relationships using Fluent API to override the default conventions and make it more maintainable.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    // configures one-to-many relationship
    modelBuilder.Entity<Book>()
        .HasRequired<Author>(b => b.Author)
        .WithMany(a => a.Books)
        .HasForeignKey<int>(b => b.AuthorId);
}
```

[Try it](https://dotnetfiddle.net/a72lbs)