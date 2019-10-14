---
PermaID: 1000122
Name: Inverse Property
---

# InverseProperty

The `InverseProperty` attribute is used to denote the inverse navigation property of a relationship when the same type takes part in multiple relationships. 

 - It is used when you need to indicate that navigation property in one class is related to the same foreign key as another navigation property in another class. 
 - For example, in the `Book` class, you may want to keep track of who wrote a book as well as who edited it. 

In the following example, we have added two new navigation properties for the 'Author' class.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author CreatedBy { get; set; }
    public Author UpdatedBy { get; set; }
}
```

The `Author` class has navigation properties back to the `Book` class, one for all of the books written by the author and one for all of the books updated by that author.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Book> BooksWritten { get; set; }
    public virtual ICollection<Book> BooksUpdated { get; set; }
}
```

The `InverseProperty` attribute is applied to the navigation properties in the `Author` class to specify their corresponding inverse navigation properties in the `Book` class.

```csharp
public class Book
{
    public int BookId { get; set; }

    public string Title { get; set; }
    public Author CreatedBy { get; set; }
    public Author UpdatedBy { get; set; }
}

public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    [InverseProperty("CreatedBy")]
    public virtual ICollection<Book> BooksWritten { get; set; }
    [InverseProperty("UpdatedBy")]
    public virtual ICollection<Book> BooksUpdated { get; set; }
}
```

[Try it](https://dotnetfiddle.net/RaZBQg)