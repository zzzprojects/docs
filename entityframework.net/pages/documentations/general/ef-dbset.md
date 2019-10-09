---
PermaID: 1000104
Name: DbSet
---

# DbSet

In Entity Framework, the DbSet class represents an entity set that can be used for the database operation, such as create, read, update, and delete.

 - The context class must include the DbSet type properties for the entities which map to database tables and views.
 - The DbSet is an implementation of the Repository pattern.

```csharp
public class BookStore : DbContext
{
    public BookStore() : base(FiddleHelper.GetConnectionStringSqlServer())
    {
    }

    public DbSet<Author> Authors { get; set; }
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

## Methods

The following are the most commonly used methods.
 
### Add

 - It adds the given entity to the context in the Added State. 
 - These entities are inserted into the database table when the SaveChanges on the context is called.

### AddRange

 - It adds the collection of entities to the context. 
 - All the entities in the collection are marked as Added State. 
 - All the entities are inserted into the database table when the SaveChanges on the context is called.
 - All the entities that are already in the context in some other state will have their state set to Added.

### Attach

 - It attaches the given entity to the context. 
 - State of the attached will be marked as Unchanged. 
 - It means calling Savechanges on the context will not affect the database

### AsNoTracking()

 - It is an extension method where the returned entities are not be tracked by the context. 
 - It means that if you want to save the entities, you need to attach them to the context and set the state correctly.

### Create()

 - It creates and returns a new instance of an entity. 
 - This entity is not added or attached to set.

### Find

 - It finds and returns an entity with the given primary key values. 
 - Context always looks for the entity in the context first and returns it if found. 
 - If it is not found, then a request is sent to the database. 
 - The entity if not found then the null is returned.

### Include(String)

 - Specifies the related objects to include in the query results.

### Remove

 - It is used to delete the entity from the database. 
 - The entity is not removed immediately, and they are marked as deleted. 
 - The entity is Deleted from the database when SaveChanges is called.

### RemoveRange

 - It is used to delete the collection of entities. 
 - It does so by changing the state of entities to the deleted state. 
 - When the SaveChanges method is invoked, Context will delete these entities from the database

### SQLQuery

 - It uses the raw SQL query to return the entities. 
 - It has two parameters. 
   - The First Parameter is the SQL query string. 
   - The second is the parameters to apply to the SQL query.