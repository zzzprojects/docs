---
Permalink: ef-core-audit-exclude-include-entity
Name: Audit - Exclude & Include Entity
LastMod: 2024-01-17
---

# Audit - Exclude & Include Entity

## Problem

You need to audit only a subset of your entities and you need to exclude/include by:

- entity types (class, base class, interface)
- entity instance
- entity entry

## Exclude/Include by Entity Type

You can exclude or include audit entry by using entity type with the 4 followings methods:

- `Exclude(Func<object, bool>)`: Exclude all entities with the same entity type as the excluded entity.
- `Exclude<T>()`: Exclude entities of type "T" or derived from type "T".
- `Include(Func<object, bool>)`: Include all entities with the same entity type as the excluded entity.
- `Include<T>()`: Include entities of type "T" or derived from type "T".

The result weither an entity type is excluded or included is cached for better performance.

{% include template-example.html %} 
```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// Globally
AuditManager.DefaultConfiguration.Exclude(x => true); // Exclude ALL
AuditManager.DefaultConfiguration.Include<IAuditable>();

// By Instance
var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
audit.Configuration.Exclude(x => true); // Exclude ALL
audit.Configuration.Include<IAuditable>();
ctx.SaveChanges(audit);

```
[Try it](https://dotnetfiddle.net/Ky4SpL)

## Exclude/Include by Entity Instance

You can exclude or include by entity instance with the 2 followings method:

- `ExcludeByInstance(Func<object, bool>)`: Exclude the current entity.
- `IncludeByInstance(Func<object, bool>)`: Include the current entity.

{% include template-example.html %} 
```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// Globally
AuditManager.DefaultConfiguration.ExcludeByInstance(x => {
	return x is CustomerExclude;
});

// By Instance
var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
audit.Configuration.ExcludeByInstance(x => {
			return x is CustomerExclude;
		});
ctx.SaveChanges(audit);
```
[Try it](https://dotnetfiddle.net/oPqtbN)

## Exclude/Include by Entity Entry

You can exclude or include by entity entry with the 2 followings method:

- `ExcludeByEntry(Func<ObjectStateEntry, bool>`: Exclude the current entity.
- `IncludeByEntry(Func<ObjectStateEntry, bool>`: Include the current entity.

Excluding/Including by entry entry give you additional flexibility as you have access to the `EntityState` and more information.

{% include template-example.html %} 
```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// Globally
AuditManager.DefaultConfiguration.ExcludeByEntry(x => {
	return x.Entity is CustomerExclude;
});

// By Instance
var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
audit.Configuration.ExcludeByEntry(x => {
			return x.Entity is CustomerExclude;
		});
ctx.SaveChanges(audit);

```
[Try it](https://dotnetfiddle.net/PDRccV)
