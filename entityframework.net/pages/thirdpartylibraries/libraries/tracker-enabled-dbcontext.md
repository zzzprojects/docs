---
PermaID: 1000003
Title: Entity Framework TrackerEnabledDbContext - Learn How to Perform Audit
MetaDescription: Unlock the power of Entity Framework by understanding how to audit your context. Learn how to audit inserted, updated, and deleted entities and save them in a log file or a database.
LastMod: 2025-06-19
Tags: third-party-library
---

# Entity Framework TrackerEnabledDbContext: Discover How to Perform Audit

## Definition

TrackerEnabledDbContext (TEDB) is a .NET library based on Entity Framework. It is created for tracking the changes in the database. 

 - This library will record new record additions, record changes & record deletions. 
 - When recording record modifications, it will audit previous and new values of the fields. 
 - It will also audit the time of change and user who changed/added/deleted the record.

## Configuration

In order to track entities, you will have to specify which entities you want to track. You can further specify which properties to track but it is optional. If you don't specify which properties to track, all properties of that entity will be tracked.

You can specify your tracking requirements in 3 ways.

 - Annotations
 - Fluent API
 - Combination of both

With the recent introduction of Fluent API, it gives you more power to change/enable/disable tracking even on runtime.

### Annotations

To track any entity, put the annotation **[TrackChanges]** to that entity. But if you don't want to track the Text property, just add the annotation **[SkipTracking]**.


```csharp
[TrackChanges]
public class Comment
{
    public int Id { get; set; }

    [SkipTracking]
    public string Text { get; set; }

    public virtual int ParentBlogId { get; set; }

    public virtual Blog ParentBlog { get; set; }
}
``` 

This entity will have 3 columns in table. Id, Text and ParentBlogId. Although entity framework works even if you don't have the property 'ParentBlogId', this library will require you to have it if you wish to track foreign keys.

### Fluent API

If you don't like to put attributes on your entities, you can use the Fluent API to configure tracking as the following example.


```csharp
EntityTracker
    .TrackAllProperties<NormalModel>()
    .Except(x => x.Description)
    .And(x => x.Id);
``` 

***Note that if you use both, annotations and fluent api, and they are conflicting for an entity or property, fluent api configuration will be considered high priority***

## Requirements

### Entity Framework Version

 - Entity Framework 6.x

[Learn more](https://github.com/bilal-fazlani/tracker-enabled-dbcontext)