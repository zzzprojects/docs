---
PermaID: 1000002
Name: Third Party Libraries
---

# Third Party Libraries

## Introduction

Entity Framework **Third Party Libraries** allow you to extend EF functionality which is not available in the official Entity Framework library, for example, auditing, caching, and filtering etc. 

## Why we need Third Party Libraries?

Entity Framework is great, but a lot of essential features is missing for some application scenarios.

The only way to achieve is either create code for this kind of scenario or use a library which fully or partially cover them.

## Supported Libraries


|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Extensions](/ef-extensions)	|PRO	|EF5<br>EF6<br>EF Core|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge|
|[Z.EntityFramework.Classic](/ef-classic)	|FREE/PRO	|EF6|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Future|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|
|[Eval Expression.NET](/eval-expression-net)	|FREE/PRO	|All	|< 1 Day	|Yes	|Dynamic Query|
|[Audit.NET](/audit-net)	|FREE	|EF6<br>EF Core	|< 1 Day	|Yes    |Audit  |
|[System.Linq.Dynamic](/system-linq-dynamic)	|FREE	|All	|< 1 Day	|Yes    |Dynamic Query  |
|[EntityFramework.Cache](/ef-cache)	|FREE	|EF6	|< 2 Days	|No	    | Cache |
|[EntityFramework.DynamicFilters](/ef-dynamic-filters)	|FREE	|EF6<br>EF Core	|< 2 Days	|Yes    |Filter |
|[Microsoft.EntityFrameworkCore.AutoHistory](/auto-history)	|FREE	|EF Core	|< 1 Day	|No	    | Audit |

## Unsuported Libraries

Use these libraries at your risk!

|Library	|Type	|EF Version	|Support	|Doc	|Features |
|:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|[AuditDbContext](/audit-dbcontext)	|FREE	|EF6	|No |Yes    |Audit  |
|[EFAuditing](/ef-auditing)	|FREE	|EF Core	|No	    |No |Audit  |
|[EFUtilities](/ef-utilities)	|FREE	|EF5<br>EF6	|No	    |No |Bulk Insert<br>Batch Delete<br>Batch Update<br>Include Optimized<br>
|[EntityFramework.Extended](/ef-extended)	|FREE	|EF5<br>EF6	|No	    |Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Future|
|[EntityFramework.Filters](/ef-filters)	|FREE	|EF6	|No	    |Yes    |Filter |
|[TrackerEnabledDbContext](/tracker-enabled-dbcontext)	|FREE	|EF6	|No	    |Yes	|Audit  |
