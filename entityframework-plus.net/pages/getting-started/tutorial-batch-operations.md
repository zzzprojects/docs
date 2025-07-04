---
Name: Getting Started with Batch Operations
---

# Batch Operations

## Batch Delete

Deletes multiple rows in a single database roundtrip and without loading entities in the context.

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
Try it: [EF Core](https://dotnetfiddle.net/KMgmZs) | [EF6](https://dotnetfiddle.net/asjI4U) 

***Support:** EF5, EF6, EF Core*

[Learn more](/batch-delete)

## Batch Update

Updates multiple rows using an expression in a single database roundtrip and without loading entities in the context.

{% include template-example.html %} 
```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// UPDATE all users inactive for 2 years
var date = DateTime.Now.AddYears(-2);
ctx.Users.Where(x => x.LastLoginDate < date)
         .Update(x => new User() { IsSoftDeleted = 1 });

```
Try it: [EF Core](https://dotnetfiddle.net/KMgmZs) | [EF6](https://dotnetfiddle.net/cV3IHD)

***Support:** EF5, EF6, EF Core*

[Learn more](/batch-update)
