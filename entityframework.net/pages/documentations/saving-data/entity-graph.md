---
PermaID: 1000043
Name: Entity Graph
---

# Entity Graph

In Entity Framework, `DbSet`provides `Add and `Attach` methods for entity graph-traversal and determine whether an entity should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).

 - These methods work well for individual entities or in cases where you don't mind all properties being included in an UPDATE statement whether they were changed or not.
 - But it is not that easy to update an entity graph in the disconnected scenario, and it needs careful design consideration.
 - The main problem is that the context doesn't know what operation was performed on a disconnected entity graph at the client side.

## Identify the State of Each Entity in the Entity Graph

To identify the entity state in the entity graph, you can use the primary key property of each entity to determine its state.

 - If a value of the key property is the default value of the CLR data type, then it is a new entity. 
 - Otherwise, it is an existing entity, and it must have already been previously saved and now needs to be updated.

In the following example, the book entity graph with the related Author and Categories entities need to be saved to the database.

```csharp
Book book = new Book()
{
    Title = "iOS Fundamentals",
    Author = new Author()
    {
        AuthorId = 1,
        Name = "Mark"
    },
    Categories = new List<Category>()
    {
        programmingCat, 
        new Category() { CategoryName = "iOS"},
        new Category() { CategoryName = "Swift"}
    }
};
```

The following `InsertOrUpdate` method saves the book entity graph which includes the related `Author` and `Categories entities. 

```csharp
public static void InsertOrUpdate(Book book)
{
    using(var context = new BookStore())
    {
        context.Entry(book).State = book.BookId == 0 ? 
            EntityState.Added : EntityState.Modified;
        
        context.Entry(book.Author).State = book.Author.AuthorId == 0 ? 
            EntityState.Added : EntityState.Modified;
    
        foreach (var cat in book.Categories)
        {
            context.Entry(cat).State = cat.CategoryId == 0 ? 
            EntityState.Added : EntityState.Modified;
        }
        
        context.SaveChanges();
    }
}
```

The context sets an appropriate state for each entity based on the key property value. 

 - If it is zero, then it sets the `Added` state; otherwise, it sets the `Modified` state. 
 - The SaveChanges() method will execute the appropriate `INSERT` or `UPDATE` command to the database for each entity.

[Try it](https://dotnetfiddle.net/QH89br)