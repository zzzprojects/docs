---
PermaID: 1000047
Title: Entity Framework Cascade Delete - Learn to Auto Delete Child Entities
MetaDescription: Unlock the power of Entity Framework by using Cascade Delete to automatically delete child-related entities without any additional code. Learn how you can make sql server delete entities implicitly.
LastMod: 2023-02-27
Tags: saving delete
---

# Entity Framework Cascade Delete: Discover to Auto Delete Child Entities

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