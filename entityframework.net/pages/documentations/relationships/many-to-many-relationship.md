---
PermaID: 1000056
Name: Many to Many
---

# Many-to-Many Relationships

In a many-to-many relationship, each row of data in one table is linked to many rows in the second table and vice versa.

 - A many-to-many relationship occurs when multiple records in one table are associated with multiple records in another table.
 - For example, a many-to-many relationship exists between `Book` and `Category` entities.

Let's say we have `Author` and `Book` entities.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
}
```
Every record in the books table is linked to one or more category records, and every record in the categories table is linked to one or more records in the books table.

## Configure Many-to-Many relationship

The many-to-many relationship creates join table because relational database systems usually don't allow a many-to-many relationship between two tables. The join table will have a composite primary key consisting combination of the primary key of both tables.

### Code First Conventions

Code First default convention creates the join table to handle the many-to-many relationship when a collection navigation properties are added in both entities.

 - For example, the `Book` class should include collection navigation property of type `Category`, and the `Category` class should contain a collection navigation property of type `Book`
 - It will create a many-to-many relationship between them without any configuration.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public virtual ICollection<Category> Categories { get; set; }
}

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
    public virtual ICollection<Book> Books { get; set; }
}
```
Entity Framework will create `Books`, `Categories` and also the joining table `CategoryBooks` in the database. The `CategoryBooks` table will include the PK (Primary Key) of both tables `Book_BookId` & `Category_CategoryId`.

<img src="https://github.com/zzzprojects/EntityFramework-FAQ/blob/master/docs2/images/many-to-many-relationship.png">

### Using Data Annotations

To configure many-to-many relationship Using Data Annotations, you need to create the Join Table in the model.

 - The Join Table `BookCategory` will have properties for the primary key of both the table. 
 - It will have two navigational properties one each for `Book` and `Category` class. 
 - The `Book` and `Category` class will have the navigational property which maps to the Join Table `BookCategory`.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public virtual ICollection<BookCategory> BookCategories { get; set; }
}

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
    public virtual ICollection<BookCategory> BookCategories { get; set; }
}

public class BookCategory
{
    [Key, Column(Order = 1)]
    public int BookId { get; set; }
    [Key, Column(Order = 2)]
    public int CategoryId { get; set; }
    public Book Book { get; set; }
    public Category Category { get; set; }
}
```

[Try it](https://dotnetfiddle.net/ZIE8r9)

It will create `Books`, `Categories` and `BookCategories` tables in the database. The `BookCategories` table will have composite key `BookId` & `CategoryId`.

### Using Fluent API

The many-to-may relationship can be achieved using `HasMany` and `WithMany` methods.

 - The default conventions for many-to-many relationships creates a joining table with the default naming conventions.
 - You can customize a joining table name and column names using Fluent API.

```csharp
modelBuilder.Entity<Book>()
   .HasMany(b => b.Categories)
   .WithMany(c => c.Books)
   .Map(cs =>
   {
       cs.MapLeftKey("BookId");
       cs.MapRightKey("CategoryId");
       cs.ToTable("BookCategories");
   });
```

[Try it](https://dotnetfiddle.net/jf4gjY)