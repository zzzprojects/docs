---
PermaID: 1000050
Name: Add or Update
---

# AddOrUpdate

A common pattern for some applications is to either add an entity as new (resulting in a database insert) or attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key. 

 - For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing. 
 - This pattern can be achieved by setting the entity state based on a check of the primary key value. 
 
```csharp
using (var context = new BookStore())
{
    List<Author> list = new List<Author>()
    {
        authorData,
        new Author() { FirstName = "Steve", LastName = "John" }
    };
    
    foreach(var author in list)
    {
        context.Entry(author).State = author.AuthorId == 0 ?
            EntityState.Added :
            EntityState.Modified;
        
        context.SaveChanges();
    }
    
    FiddleHelper.WriteTable(context.Authors.ToList());
}
```

[Try it](https://dotnetfiddle.net/Y1BieQ)