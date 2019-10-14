---
PermaID: 1000044
Name: Entity Disconnected
---

# Entity Disconnected

## Introduction

A `DbContext` instance automatically tracks entities returned from the database, and any changes made to these entities are detected when SaveChanges is called, and the database is also updated as needed.

 - Sometimes entities are queried using one context instance and then saved using a different instance. 
 - In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).

## Adding New Entity

A new entity can be added to the context by calling the `DbSet.Add` method. 

 - It will put the entity into the `Added` state. 
 - The entity will be inserted into the database when `SaveChanges` is called.

```csharp
using(var context = new BookStore())
{
    Author author = new Author()
    {
        //code here
    };
    
    context.Authors.Add(author);
    Console.WriteLine("Entity State: {0}", context.Entry(author).State);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/UfqF0H)

Another way to add a new entity to the context is to change its state to `Added`. 

```csharp
using(var context = new BookStore())
{
    Author author = new Author()
    {
        //code here
    };
    
    context.Entry(author).State = EntityState.Added;
    Console.WriteLine("Entity State: {0}", context.Entry(author).State);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/qhEyCf)

## Attaching Existing Entity

If you have an entity that you know already exists in the database but which is not currently being tracked by the context.

 - You can tell the context to track the entity using the `DbSet.Attach`. 
 - The entity will be in the Unchanged state in the context.

```csharp
using(var context = new BookStore())
{
    context.Authors.Attach(existingAuthor);
    Console.WriteLine("Entity State: {0}", context.Entry(existingAuthor).State);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/nrT0SA)

Another way to attach an existing entity to the context is to change its state to `Unchanged`. 

```csharp
using(var context = new BookStore())
{
    context.Entry(existingAuthor).State = EntityState.Unchanged;
    Console.WriteLine("Entity State: {0}", context.Entry(existingAuthor).State);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/vjsUdn)

## Attaching Existing Modified Entity

If you have an entity that already exists in the database, but you made some changes, you can tell the context to attach the entity and set its state to `Modified`.

```csharp
using(var context = new BookStore())
{
    context.Entry(existingAuthor).State = EntityState.Modified;
    Console.WriteLine("Entity State: {0}", context.Entry(existingAuthor).State);
    context.SaveChanges();
}
```

[Try it](https://dotnetfiddle.net/KAL6iB)

When you change the state to `Modified` all the properties of the entity will be marked as modified and all the property values will be sent to the database when `SaveChanges` is called.

## How EF Determine New or Existing Entities

It is necessary to determine whether to insert or update an entity. The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.

 - If the key has not been set, then the entity must be new and needs to be inserted. 
 - On the other hand, if the key value has been set, then it must have already been previously saved and now needs to be updated. 

```csharp
public void InsertOrUpdate(Author author)
{
    using (var context = new BookStore())
    {
        context.Entry(author).State = author.AuthorId == 0 ?
            EntityState.Added :
            EntityState.Modified;

        context.SaveChanges();
    }
}
```

[Try it](https://dotnetfiddle.net/zy0BUF)