---
Permalink: ef-core-batch-delete
Name: Batch Delete
---

# Batch Delete

## Description

Deleting using Entity Framework Core can be very slow if you need to delete hundreds or thousands of entities. Entities are first loaded in the context before being deleted which is very bad for the performance and then, they are deleted one by one which makes the delete operation even worse.

**EF+ Batch Delete** deletes multiple rows in a single database roundtrip and without loading entities in the context.

{% include template-example.html %} 
```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// DELETE all users inactive for 2 years
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Delete();

// DELETE using a BatchSize
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Delete(x => x.BatchSize = 1000);

```
[Try it](https://dotnetfiddle.net/8qqyPw)

## Scenarios
 
 - [Query Criteria](#query-criteria)
 - [Batch Size](#batch-size)
 - [Batch Delay Interval](#batch-delay-interval)
 - [Executing Interceptor](#executing-interceptor)
 - [EF Core InMemory](#ef-core-inmemory)
 
### Query Criteria

The **Delete** IQueryable extension methods deletes rows matching the query criteria without loading entities in the context.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// DELETE all users
ctx.Users.Delete();

// DELETE all users inactive for 2 years
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Delete();

```

[Try it](https://dotnetfiddle.net/HQauA0)

[Try it (Async version)](https://dotnetfiddle.net/Xdxjg1)

### Batch Size

The **BatchSize** property sets the amount of rows to delete in a single batch.

Default Value = 4000


{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Delete(x => x.BatchSize = 1000);

```
[Try it](https://dotnetfiddle.net/CpzGUz)

### Batch Delay Interval

The **BatchDelayInterval** property sets the amount of time (in milliseconds) to wait before starting the next delete batch.

Default Value = 0

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// Pause 2 seconds between every batch
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Delete(x => x.BatchDelayInterval = 2000);

```
[Try it](https://dotnetfiddle.net/3iRApA)

### Executing Interceptor

The **Executing** property intercepts the DbCommand with an action before being executed.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

string commandText
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Delete(x => { x.Executing = command => commandText = command.CommandText; });

```
[Try it](https://dotnetfiddle.net/KrtT0e)
 
### EF Core InMemory

Specify a DbContext factory in the **BatchDeleteManager** to enable BatchDelete with In Memory. A DbContext factory must be

{% include template-example.html %} 
```csharp

// Options
var db = new DbContextOptionsBuilder();
db.UseInMemoryDatabase();

// Specify InMemory DbContext Factory
BatchDeleteManager.InMemoryDbContextFactory = () => new MyContext(db.Options);

// Use the same code as with Relational Database
var _context = new MyContext(db.Options);
_context.Foos.Delete();

```

## Conclusion

**EF+ Batch Delete** is the most efficient way to delete records. You drastically improve your application performance by removing the need to retrieve and load entities in your context and by performing a single database roundtrip instead of one for every record.

Need help getting started? [info@zzzprojects.com](mailto:info@zzzprojects.com)

We welcome all comments, ideas and suggestions to improve our library.
