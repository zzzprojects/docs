---
PermaID: 1000047
Name: Cascade Delete
---

# Cascade Delete

Cascade delete allows the deletion of a row to trigger the deletion of related rows automatically.

 - EF will delete a child record(s) (database rows) automatically when its parent is explicitly deleted via the DbContext.
 - For example, when an author is deleted, it will remove all the related books automatically as well.  
 
```csharp
using (var context = new BookStore())
{    
    var author = context.Authors
        .FirstOrDefault();
    
    context.Authors.Remove(author);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/aIifq8)

## Turn off Cascade Delete

Using Fluent API, you can configure entities to turn off cascade delete by calling the `WillCascadeOnDelete(false)` method.

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasOptional<Author>(b => b.Author)
        .WithMany()
        .WillCascadeOnDelete(false);
}
```

Now when the author entity is deleted, the related books won't be deleted from the database.

[Try it](https://dotnetfiddle.net/IRjY16)