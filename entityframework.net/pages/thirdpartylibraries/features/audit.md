---
PermaID: 1000028
Name: Audit
---

# Audit

## Introduction

The audit allows you to log changes from Entity Framework change tracker in a database or log file. 

## Why Audit?

Entity Framework saves entities in a database but doesn't let you easily track changes. To follow all the changes, you will need to use a third-party library.

### Common scenario:

 - To keep a history of changes in a database or a log file
 - Track What/Who/When a modification occurs
 - Show historical data changes in a grid

## Google Related Questions

 - [Entity Framework Audit Trail](https://www.google.com/search?q=entity+framework+audit+trail)
 - [Entity Framework Audit Fields](https://www.google.com/search?q=entity+framework+audit+fields)
 - [Entity Framework Log Changes to Database](https://www.google.com/search?q=entity+framework+log+changes+to+database)

## StackOverflow Related Questions

 - [EF6 Code First Audit Table Per Entity](https://stackoverflow.com/questions/30714466/ef6-code-first-audit-table-per-entity)
 - [Entity Framework 6: audit/track changes](https://stackoverflow.com/questions/26355486/entity-framework-6-audit-track-changes)
 - [How to create an audit trail with Entity Framework 5 and MVC 4](https://stackoverflow.com/questions/20961489/how-to-create-an-audit-trail-with-entity-framework-5-and-mvc-4)
 - [Does EF 6 and higher offer better ways of auditing interactions with the data?](https://stackoverflow.com/questions/37446134/does-ef-6-and-higher-offer-better-ways-of-auditing-interactions-with-the-data)
 - [EF Auditing Creations](https://stackoverflow.com/questions/35221339/ef-auditing-creations)
 - [Audit Log to Maintain History Against Records using Repository Pattern.](https://stackoverflow.com/questions/35177121/audit-log-to-maintain-history-against-records-using-repository-pattern)


```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// AutoSave audit in a database
AuditManager.DefaultConfiguration.AutoSavePreAction = (context, audit) =>
         (context as EntityContext).AuditEntries.AddRange(audit.Entries);

var ctx = new EntityContext();
// ... ctx changes ...

var audit = new Audit();
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

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|
|[Audit.NET](/audit-net)	|FREE	|EF6<br>EF Core	|< 1 Day	|Yes    |Audit  |
|[Microsoft.EntityFrameworkCore.AutoHistory](/auto-history)	|FREE	|EF Core	|< 1 Day	|No	    | Audit |

## Unsuported Libraries

Use these libraries at your risk!

|Library	|Type	|EF Version	|Support	|Doc	|Features |
|:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|[AuditDbContext](/audit-dbcontext)	|FREE	|EF6	|No |Yes    |Audit  |
|[EFAuditing](/ef-auditing)	|FREE	|EF Core	|No	    |No |Audit  |
|[EntityFramework.Extended](/ef-extended)	|FREE	|EF5<br>EF6	|No	    |Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Future|
|[TrackerEnabledDbContext](/tracker-enabled-dbcontext)	|FREE	|EF6	|No	    |Yes	|Audit  |