---
PermaID: 1000041
Name: Save Changes
---

# SaveChanges

In Entity Framework, the `DbContext.SaveChanges` method saves all changes made in the context of the database. 

 - You can add, modify, and remove data using your context and entity classes.
 - `SaveChanges` method automatically call `DetectChanges` method to discover any changes to entity instances before saving to the underlying database. 

## Adding Data

To add data to the database, you can use the `DbSet.Add` method to add new instances of your entity classes, and then call `SaveChanges`.

```csharp
using (var context = new BookStore())
{
    Author author = new Author()
    {
        FirstName = "Mark",
        LastName = "Johny",
    };
    
    context.Authors.Add(author);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/oKrcSA)

## Updating Data

When you made changes to any existing entity, or simply modify the values assigned to properties and then call `SaveChanges`, it will update the data into a database.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .FirstOrDefault();

    author.LastName = "Cuban";
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/jsrNFd)

## Deleting Data

You can use the `DbSet.Remove` method to delete instances of your entity classes. 

 - If the entity already exists in the database, it will be deleted during `SaveChanges`. 
 - If the entity has not yet been saved to the database, then it will be removed from the context and will no longer be inserted when `SaveChanges` is called.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .Where(a => a.AuthorId == 2)
        .FirstOrDefault();
            
        context.Authors.Remove(author);
        context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/g8lQE2)