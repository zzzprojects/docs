---
PermaID: 1000073
Name: EF Performance
---

# Improve Entity Framework Performance

## Improve Entity Framework Performance

 - Is Entity Framework as slow as some people report it?  Yes and no.
 - Entity Framework offers great advantages over other ORM, and it's in fact pretty fast.

### Problem

 - It's too easy to fall into a Entity Framework performance pitfall. 
 - Sooner or later, many developers make some mistakes which may impact the application performance severely.

### Performance Cheat Sheet

|Problem	                                            |Solution                                                                       |
|:----------------------------------------------------- |:----------------------------------------------------------------------------- |
|[Add](/improve-ef-add-performance)                     |Use AddRange<br>Disable AutoDetectChanges<br>Add in Batches                    |
|Change Tracker                                         |Track Less Entities                                                            |
|[DetectChanges](/improve-ef-detect-changes-performance)|Disable AutoDetectChanges<br>SaveChanges in Batches                            |
|Eager Loading                                          |Use AsNoTracking<br>Use EF+ Caching<br>Use EF+ Query Future                    |
|Lazy Loading                                           |Use Eager Loading                                                              |
|[Include](/improve-ef-include-performance)             |Split in multiple Include<br>Use EF+ IncludeFilter<br>Use EF+ IncludeOptimized |
|[Insert](/improve-ef-include-performance)              |Use EFE BulkInsert                                                             |
|Query                                                  |Use AsNoTracking<br>Use EF+ Caching<br>Use EF+ Query Future                    |
|[SaveChanges](/improve-ef-save-changes-performance)    |Use EFE Bulk Operations<br>Use EFE BulkSaveChanges                             |

### Recommendation

Have you read all theses articles? Great! Read them all a second or a third time if necessary.

These articles are still under development. We recommend you to subscribe to our [newsletter](http://zzzprojects.us9.list-manage.com/subscribe?u=cecbc4775cf67bf1ff82018af&id=4765ffa5f8) to stay updated.