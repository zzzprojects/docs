---
PermaID: 1000007
Name: EF Plus
---

# Plus

## Definition

**Entity Framework Plus** is a library that improves Entity Framework performance and overcomes limitations with **MUST-HAVE** features.

## Features

- Batch Operations
  - [Batch Delete](http://entityframework-plus.net/batch-delete)
  - [Batch Update](http://entityframework-plus.net/batch-update)
- LINQ
  - [LINQ Dynamic](http://entityframework-plus.net/linq-dynamic)
- Query
  - [Cache](http://entityframework-plus.net/query-cache)
  - [Query Deferred](http://entityframework-plus.net/query-deferred)
  - [Query DbSetFilter](http://entityframework-plus.net/query-db-set-filter)
  - [Query Filter](http://entityframework-plus.net/query-filter)
  - [Query Future](http://entityframework-plus.net/query-future)
  - [Query IncludeFilter](http://entityframework-plus.net/query-include-filter)
  - [Query IncludeOptimized](http://entityframework-plus.net/query-include-optimized)
- Audit
  - [Audit](http://entityframework-plus.net/audit)

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

[Learn more](http://entityframework-plus.net/)