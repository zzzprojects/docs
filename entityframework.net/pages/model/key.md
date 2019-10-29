---
PermaID: 1000121
Name: Key
---

# Key

The `Key` attribute can be applied to a property to make it a key property in an entity class and the corresponding column to a primary key column in the database. 

 - The default Code First convention look for a property named **"Id"**, or a combination of **"class name"** and **"Id"**, such as **BookId**. 
 - This property will map to a primary key column in the database.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}

public class Category
{
    public int CatId { get; set; }
    public string CategoryName { get; set; }
}
```

The `Book` class follow the default code first conventions, and the name of the key field in the `Category` class does not meet the Entity Framework naming convention for entity keys.

 - If code first does not find a property that matches the default convention, it will throw an exception because Entity Framework requires a key property. 
 - The `Key` attribute overrides this default convention, you can use the key annotation to specify which property is to be used as the EntityKey.

```csharp
public class Category
{
    [Key]
    public int CatId { get; set; }
    public string CategoryName { get; set; }
}
```

[Try it](https://dotnetfiddle.net/kQAyYp)

## Composite keys

A primary key that consists of more than one property. For example, a `BookCategory` entity class has a primary key which contains `BookId` and `CategoryId`.

```csharp
public class BookCategory
{
    [Key]
    public int BookId { get; set; }
    [Key]
    public int CategoryId { get; set; }
    public Book Book { get; set; }
    public Category Category { get; set; }
}
```

Using the above example would result in an `InvalidOperationException`.

> System.InvalidOperationException: 'Unable to determine composite primary key ordering for type 'BookCategory'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary 

[Try it](https://dotnetfiddle.net/k6kTIb)

Entity Framework requires you to define an order for the key properties. You can do this by using the Column annotation to specify an order.

```csharp
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
[Try it](https://dotnetfiddle.net/LIp2Xh)