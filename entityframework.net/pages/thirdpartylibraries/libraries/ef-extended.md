---
PermaID: 1000009
Name: EF Extended
---

# Extended

## Definition

**EntityFramework.Extended** is a library that improves Entity Framework performance and overcomes limitations with **MUST-HAVE** features.

**IMPORTANT:** This library is no longer supported since 2015. We highly recommend you to move to [Entity Framework Plus](/ef-plus).

## Features

 - Batch Update and Delete
 - Future Queries
 - Query Result Cache
 - Audit Log

## Batch Update and Delete

A current limitation of the Entity Framework is that to update or delete an entity you have first to retrieve it into memory. Now in most scenarios, this is just fine. There are however some scenarios where performance would suffer. Also, for single deletes, the object must be retrieved before it can be deleted requiring two calls to the database. Batch update and delete eliminate the need to retrieve and load an entity before modifying it.

### Deleting


```csharp
//delete all users where FirstName matches
context.Users
    .Where(u => u.FirstName == "firstname")
    .Delete();
```

### Update


```csharp
//update all tasks with status of 1 to status of 2
context.Tasks
    .Where(t => t.StatusId == 1)
    .Update(t => new Task { StatusId = 2 });

//example of using an IQueryable as the filter for the update
var users = context.Users.Where(u => u.FirstName == "firstname");
context.Users.Update(users, u => new User {FirstName = "newfirstname"});
```

## Future Queries

Future queries are created with the following extension methods...

 - Future()
 - FutureFirstOrDefault()
 - FutureCount()


```csharp
// build up queries
var q1 = db.Users
    .Where(t => t.EmailAddress == "one@test.com")
    .Future();

var q2 = db.Tasks
    .Where(t => t.Summary == "Test")
    .Future();

// this triggers the loading of all the future queries
var users = q1.ToList();
```

## Query Result Cache

To cache query results, use the FromCache extension method. Below is a sample caching query results. Just construct the LINQ query as you usually would, then append the FromCache extension.


```csharp
//query is cached using the default settings
var tasks = db.Tasks
    .Where(t => t.CompleteDate == null)
    .FromCache();

//query result is now cached 300 seconds
var tasks = db.Tasks
    .Where(t => t.AssignedId == myUserId && t.CompleteDate == null)
    .FromCache(CachePolicy.WithDurationExpiration(TimeSpan.FromSeconds(300)));
```

## Audit Log

The Audit Log feature will capture the changes to entities anytime they are submitted to the database. 

 - The Audit Log captures only the entities that are changed and only the properties of those entities that were changed. 
 - The before and after values are recorded. 
 - AuditLogger.LastAudit is where this information is held, and there is a ToXml() method that makes it easy to turn the AuditLog into XML for easy storage.

The AuditLog can be customized via attributes on the entities or via a Fluent Configuration API.

### Fluent Configuration


```csharp
// config audit when your application is starting up...
var auditConfiguration = AuditConfiguration.Default;

auditConfiguration.IncludeRelationships = true;
auditConfiguration.LoadRelationships = true;
auditConfiguration.DefaultAuditable = true;

// customize the audit for Task entity
auditConfiguration.IsAuditable<Task>()
    .NotAudited(t => t.TaskExtended)
    .FormatWith(t => t.Status, v => FormatStatus(v));

// set the display member when status is a foreign key
auditConfiguration.IsAuditable<Status>()
    .DisplayMember(t => t.Name);
```

### Create an Audit Log


```csharp
var db = new TrackerContext();
var audit = db.BeginAudit();

// make some updates ...

db.SaveChanges();
var log = audit.LastLog;
```

## Requirements

### Entity Framework Version

 - Entity Framework 6.x
 - Entity Framework 5

[Learn more](https://github.com/zzzprojects/EntityFramework.Extended)