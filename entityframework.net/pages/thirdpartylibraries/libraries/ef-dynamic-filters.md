---
PermaID: 1000010
Title: Entity Framework Dynamic Filters - Learn How to Filter Your Query
MetaDescription: Unlock the power of Entity Framework by understanding how to filter your query automatically. Learn how to pre-filter your query to handle advanced scenarios such as tenant id.
LastMod: 2025-06-19
Tags: third-party-library
---

# Entity Framework Dynamic Filters: Discover How to Filter Your Query

## Definition

**Entity Framework Dynamic Filters** is a library that creates global and scoped filters for Entity Framework queries.

The filters are automatically applied to every query and can be used to support use cases such as Multi-Tenancy, Soft Deletes, Active/Inactive, etc.

## How It Works?

The library will add extensions methods automatically to DbContext and DbModelBuilder classes to access **Dynamic Filters**.

- Filters are defined in DbContext.OnModelCreating().
- Filters can be created using boolean LINQ expressions.
- It also supports the Contains() operator to define filters.

Filters can be defined on a specific entity class or an interface by providing a specific value.

{% include template-example.html%} 
```csharp
modelBuilder.Filter("IsDeleted", (ISoftDelete d) => d.IsDeleted, false);
```

This filter will apply to all classes that implement ISoftDelete.

## Enabling and Disabling Filters

Filter can be easily disabled by using the DbContext.DisableFilter extension method. To disable a filter globally, use DbModelBuilder.DisableFilterGlobally method after it is created in OnModelCreating

{% include template-example.html%} 
```csharp
context.DisableFilter("IsDeleted");

//Disable a filter globally
modelBuilder.DisableFilterGlobally("IsDeleted");
```

You can also enable/disable all filters at once within a context. 

{% include template-example.html%} 
```csharp
context.DisableAllFilters();
context.EnableAllFilters();
```

## Conditionally Enabling Filters

Filters can also be enabled conditionally and you will even need to define those conditions along with your filter definition.

{% include template-example.html%} 
```csharp
modelBuilder.Filter("BlogEntryFilter", (BlogEntry b, Guid accountID) => (b.AccountID == accountID), 
                    () => GetPersonIDFromPrincipal(Thread.CurrentPrincipal));
modelBuilder.EnableFilter("BlogEntryFilter", () => !UserIsAdmin(Thread.CurrentPrincipal));
```

In this example, a filter is defined on BlogEntry records to restrict to only the current AccountID. But the filter will only be enabled if the user is not an Admin user.

## Requirements

### Entity Framework Version

 - Entity Framework Core 2.x
 - Entity Framework 6.x

[Learn more](https://entityframework-dynamicfilters.net/overview)