---
Permalink: batch-update
CanonicalLink: https://entityframework-plus.net/ef-core-batch-update
Name: Batch Update
---

# Batch Update

## Description

Updating using Entity Framework can be very slow if you need to update hundreds or thousands of entities with the same expression. Entities are first loaded in the context before being updated which is very bad for the performance and then, they are updated one by one which makes the update operation even worse.

**EF+ Batch Update** updates multiple rows using an expression in a single database roundtrip and without loading entities in the context.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// UPDATE all users inactive for 2 years
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Update(x => new User() { IsSoftDeleted = 1 });

```
[Try it](https://dotnetfiddle.net/uzsdub)

## Scenarios
 
 - [Query Criteria](#query-criteria)
 - [Executing Interceptor](#executing-interceptor)

### Query Criteria

The **Update** IQueryable extension method updates rows matching the query criteria with an expression without loading entities in the context.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// UPDATE all users inactive for 2 years
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Update(x => new User() { IsSoftDeleted = 1 });

```

[Try it](https://dotnetfiddle.net/sfMLRj)

[Try it (Async version)](https://dotnetfiddle.net/7fHg1g)

### Executing Interceptor

The **Executing** property intercepts the DbCommand with an action before being executed.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

string commandText
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Update(x => new User() { IsSoftDeleted = 1 },
                 x => { x.Executing = command => commandText = command.CommandText; });

```
[Try it](https://dotnetfiddle.net/a6zJUe)

## Conclusion

**EF+ Batch Update** is the most efficient way to update records using an expression. You drastically improve your application performance by removing the need to retrieve and load entities in your context and by performing a single database roundtrip instead of making one for every record.

Need help getting started? [info@zzzprojects.com](mailto:info@zzzprojects.com)

We welcome all comments, ideas and suggestions to improve our library.
