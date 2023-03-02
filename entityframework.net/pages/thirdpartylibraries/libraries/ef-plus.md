---
PermaID: 1000007
Title: Entity Framework Plus - Learn About the Most Popular Libraries
MetaDescription: Unlock the power of Entity Framework by discovering new methods to use with EF6. Learn to extend EF6 by adding functionalities like auditing and batching.
LastMod: 2023-02-22
Tags: third-party-library
---

# Entity Framework Plus: Discover About the Most Popular Libraries

## Definition

**Entity Framework Plus** is a library that improves Entity Framework performance and overcomes limitations with **MUST-HAVE** features.

## Features

- Batch Operations
  - [Batch Delete](https://entityframework-plus.net/batch-delete)
  - [Batch Update](https://entityframework-plus.net/batch-update)
- LINQ
  - [LINQ Dynamic](https://entityframework-plus.net/linq-dynamic)
- Query
  - [Cache](https://entityframework-plus.net/query-cache)
  - [Query Deferred](https://entityframework-plus.net/query-deferred)
  - [Query DbSetFilter](https://entityframework-plus.net/query-db-set-filter)
  - [Query Filter](https://entityframework-plus.net/query-filter)
  - [Query Future](https://entityframework-plus.net/query-future)
  - [Query IncludeFilter](https://entityframework-plus.net/query-include-filter)
  - [Query IncludeOptimized](https://entityframework-plus.net/query-include-optimized)
- Audit
  - [Audit](https://entityframework-plus.net/audit)

## Batch Operations

Batch Operations method allow performing **UPDATE** or **DELETE** operation directly in the database using a LINQ Query without loading entities in the context.

Everything is executed on the database side to let you get the best performance available.


```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// DELETE all users which has been inactive for 2 years
ctx.Users.Where(x => x.LastLoginDate < DateTime.Now.AddYears(-2))
         .Delete();

// DELETE using a BatchSize
ctx.Users.Where(x => x.LastLoginDate < DateTime.Now.AddYears(-2))
         .Delete(x => x.BatchSize = 1000);
```

## Audit

Allow to track changes easily, exclude/include entity or property, and autosave audit entries in the database.


```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

var ctx = new EntityContext();
// ... ctx changes ...

var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
ctx.SaveChanges(audit);

// Access to all auditing information
var entries = audit.Entries;
foreach(var entry in entries)
{
    foreach(var property in entry.Properties)
    {
    }
}
```

AutoSave audit in your database


```csharp
AuditManager.DefaultConfiguration.AutoSavePreAction = (context, audit) =>
    (context as EntityContext).AuditEntries.AddRange(audit.Entries);
```

## Requirements

### Entity Framework Version

 - Entity Framework Core 2.x
 - Entity Framework 6.x
 - Entity Framework 5

[Learn more](https://entityframework-plus.net/)