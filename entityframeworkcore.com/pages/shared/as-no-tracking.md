---
permaid_EF6: 1000065
permaid_EFCore: 1000231
Permalink_EF6: draft-as-no-tracking
Permalink_EFCore: draft-querying-data-asnotracking
name: AsNoTracking
tags: query change-tracker disconnected
---

# [EF_Version] - As No Tracking

_(This page is currently in draft mode. The final content should be available within a few days.)_

## What is the [EF_Version] AsNoTracking method?

The `AsNoTracking()` method make the LINQ query to return entities but without added them in the `ChangeTracker`. So those entities are not tracked and are known as `Disconnected Entities`.

```csharp
// Simple example with Where clause
```

## Performance Comparison

Not adding entities in the Change Tracker come with some performance enhancement for the current query but also for future queries that will be performed from the same context.

Always remember, more the `ChangeTracker` contains entities, slower it dramatically becomes.
	Without AsNoTracking	With AsNoTracking
10		
100		
1000		
5000		
Try it

## When should I use AsNoTracking?

Honestly, if the `ChangeTracker` contains or will contains only a few hundreds of entities or less, it simply doesn’t matter.

The performance improvement doesn’t worth all benefits which come with the `ChangeTracker` or simply the trouble to ask the question to yourself if you should use the `AsNoTracking` method or not.

However, if you return a large amount of entities, then you think about using the `AsNoTracking` method whenever you do a read operation to disconnect those entities.

---

## FAQ

- What
   - [What AsNoTracking PROS & CONS?](#what-asnotracking-pros-cons)
   - [What AsNoTracking do under the hood?](#what-asnotracking-do-under-the-hood)
- How
   - [How AsNoTracking is different from AutoDetectChangesEnabled?](#how-asnotracking-is-different-from-autodetectchangesenabled)
   - [How to use AsNoTracking and Attach an entity?](#how-to-use-asnotracking-and-attach-an-entity)
   - [How to use AsNoTracking and Delete an entity?](#how-to-use-asnotracking-and-delete-an-entity)
   - [How to use AsNoTracking and Insert an entity?](#how-to-use-asnotracking-and-insert-an-entity)
   - [How to use AsNoTracking and Update an entity?](#how-to-use-asnotracking-and-update-an-entity)
   - [How to use AsNoTracking by default/global?](#how-to-use-asnotracking-by-defaultglobal)
   - [How to use AsNoTracking with Anonymous Type?](#how-to-use-asnotracking-with-anonymous-type)
   - [How to use AsNoTracking with Find?](#how-to-use-asnotracking-with-find)
   - [How to use AsNoTracking with FromSql?](#how-to-use-asnotracking-with-fromsql)
   - [How to use AsNoTracking with FromSqlRaw?](#how-to-use-asnotracking-with-fromsqlraw)
   - [How to use AsNoTracking with FromSqlRawInterpolated?](#how-to-use-asnotracking-with-fromsqlrawinterpolated)
   - [How to use AsNoTracking with Include?](#how-to-use-asnotracking-with-include)
   - [How to use AsNoTracking with Join?](#how-to-use-asnotracking-with-join)
   - [How to use AsNoTracking with Lazy Loading?](#how-to-use-asnotracking-with-lazy-loading)
   - [How to use AsNoTracking with LINQ? Before or After Where?](#how-to-use-asnotracking-with-linq-before-or-after-where)
   - [How to use AsNoTracking with Mock?](#how-to-use-asnotracking-with-mock)
   - [How to use AsNoTracking with Projection?](#how-to-use-asnotracking-with-projection)
   - [How to use AsNoTracking with RepositoryPattern?](#how-to-use-asnotracking-with-repositorypattern)
   - [How to use AsNoTracking with SaveChanges?](#how-to-use-asnotracking-with-savechanges)
   - [How to use AsNoTracking with Stored Procedure?](#how-to-use-asnotracking-with-stored-procedure)
#begin_EF6
   - [How to use AsNoTracking with SqlQuery?](#how-to-use-asnotracking-with-stored-procedure)
#end_EF6
- Why
   - [Why (Problem / Cause / Issue)](#why-problem-cause-issue)
   - [Why AsNoTracking is slow?](#why-asnotracking-is-slow)
   - [Why AsNoTracking is not working?](#why-asnotracking-is-not-working)
#begin_EF6
   - [Why AsNoTracking is not available/missing/cannot be found?](#why-asnotracking-is-not-availablemissingcannot-be-found)
#end_EF6
   - [Why AsNoTracking throw an identity issue?](#why-asnotracking-throw-an-identity-issue)

---

## What AsNoTracking PROS & CONS?

### Pros

- Improved performance over regular LINQ queries.
- Fully materialized objects.
- Simplest to write with syntax built into the programming language. 
- Improve performance of future tracked queries.

### Cons

- Not suitable for CUD operations.
- Certain technical restrictions, such as: Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.
- Doesn’t associate related entities already existing in the Change Tracker
- You still can’t use LIKE with general pattern matching.

## What AsNoTracking do under the hood?

Basically, the tracking and no tracking queries does the same except the part from the Change Tracker.

If you query which have related item already loaded in the `ChangeTracker`, if the query is tracked, the `ChangeTracker` will automatically set the navigation properties of those entities. However, if they are not tracked, the `ChangeTracker` will do nothing. 

---

## How AsNoTracking is different from AutoDetectChangesEnabled?

They are totally different!

The `AsNoTracking` method return disconnected entities. The performance is improved when querying entities.

The `AutoDetectChangesEnabled` doesn’t automatically check if a modification has been made on connected entities. The performance is improved when saving entities.

So both behavior are completely different.
## How to use AsNoTracking and Attach an entity?
In [EF_Version], when doing a LINQ query with the `AsNoTracking` method, all entities are not part of the `ChangeTracker`.

So, if you want to delete or update an entity, you need first to `Attach` it to the context and change the `EntityState` to Modified or Deleted.

```csharp
// Example (Modified + Deleted)
```

NOTE: When performing an update, [EF_Version] doesn’t know about initial value. Therefore, all properties will be considered as modified.

## How to use AsNoTracking and Delete an entity?

In [EF_Version], when doing a LINQ query with the `AsNoTracking` method, all entities are not part of the `ChangeTracker`.

So, if you want to delete an entity, you need first to `Attach` it to the context and change the `EntityState` to Deleted.

```csharp
// Example (Deleted)
```

## How to use AsNoTracking and Insert an entity?

In [EF_Version], when doing a LINQ query with the `AsNoTracking` method, all entities are not part of the `ChangeTracker`.

Usually, trying to insert a disconnected entity doesn’t make sense as the entity already exists in the database but in some case, you want to insert it in a different database which now make sense.

```csharp
// Example (Inserted)
```

## How to use AsNoTracking and Update an entity?

In [EF_Version], when doing a LINQ query with the `AsNoTracking` method, all entities are not part of the `ChangeTracker`.

So, if you want to update an entity, you need first to `Attach` it to the context and change the `EntityState` to Modified

```csharp
// Example (Modified)
```

NOTE: When performing an update, [EF_Version] doesn’t know about initial value. Therefore, all properties will be considered as modified.

## How to use AsNoTracking by default/global?

You can globally disable the tracking by setting the `ChangeTracker.QueryTrackingBehavior` to `QueryTrackingBehavior.NoTracking`. 

```csharp
// context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
```

## How to use AsNoTracking with Anonymous Type?

In [EF_Version], if you make a LINQ Query and return anonymous entities, the anonymous entities is already not tracked. So, you don’t have to use the `AsNoTracking` method.

It will not make sense to track those entities as there is no way to save it back to the database.

## How to use AsNoTracking with Find?

This scenario usually doesn’t make sense as the goal of the `Find` method is to return the entity from the `ChangeTracker` if it has been already loaded.

Some people recommend to use the Find method and detach the entity, but it doesn’t make sense either and can cause some unexpected behavior as this entity will not be longer tracker, nor saved if there is a modification. (ref: https://stackoverflow.com/a/46223929/5619143)

## How to use AsNoTracking with FromSql?

You can apply the `AsNoTracking` method with `FromSql` in the same way you do it with LINQ query.

```csharp
// Example (FromSql)
```

## How to use AsNoTracking with FromSqlRaw?

You can apply the `AsNoTracking` method with `FromSqlRaw` in the same way you do it with LINQ query.

```csharp
// Example (FromSqlRaw)
```

## How to use AsNoTracking with FromSqlRawInterpolated?

You can apply the `AsNoTracking` method with `FromSqlRawInterpolated` in the same way you do it with LINQ query.

```csharp
// Example (FromSqlInterpolated)
```

## How to use AsNoTracking with Include?

You can apply the `AsNoTracking` method with `Include` as you normally do it with LINQ query.

All entities returned will not be tracked which include parents and childs.

```csharp
// Example (parent + child)
```

## How to use AsNoTracking with Join?

## How to use AsNoTracking with Lazy Loading?

## How to use AsNoTracking with LINQ? Before or After Where?

It doesn’t really matter where you call the `AsNoTracking`. It can be at the start, in middle or at the end before calling an immediate method such as `ToList()`.

In all cases, the same behavior will happen. All entities will not part of the `ChangeTracker`.

## However, we recommend you to put it at the end before the immediate method for consistence reason.

```csharp
// Example (re-use the first example)
```

## How to use AsNoTracking with Mock?

TBD, we don't know either!

## How to use AsNoTracking with Projection?

In [EF_Version], if you make a LINQ Query and return a projection, the projection is already not tracked. So, you don’t have to use the `AsNoTracking` method.

## How to use AsNoTracking with RepositoryPattern?

TODO: We must read about repository pattern

## How to use AsNoTracking with SaveChanges?

In [EF_Version], when doing a LINQ query with the `AsNoTracking` method, all entities are not part of the `ChangeTracker`.

So, if you want to use SaveChanges to delete or update an entity, you need first to `Attach` it to the context and change the `EntityState` to Modified or Deleted.

```csharp
// Example (Modified + Deleted)
```

NOTE: When performing an update, [EF_Version] doesn’t know about initial value. Therefore, all properties will be considered as modified.

## How to use AsNoTracking with Stored Procedure?

You can apply the `AsNoTracking` method with `Stored Procedure` in the same way you do it with LINQ query.

```csharp
// Example (Stored Procedure)
```

#begin_EF6
## How to use AsNoTracking with SqlQuery?

You can apply the `AsNoTracking` method with `SqlQuery` in the same way you do it with LINQ query.

```csharp
// Example (SqlQuery)
```
#end_EF6
---

## Why (Problem / Cause / Issue)

TODO

## Why AsNoTracking is slow?

TODO

## Why AsNoTracking is not working?

TODO
#begin_EF6
## Why AsNoTracking is not available/missing/cannot be found?

TODO
#end_EF6

## Why AsNoTracking throw an identity issue?

TODO

