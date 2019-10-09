---
PermaID: 1000008
Name: EF Filters
---

# Filters

## Definition

Filters allow you to define a parameterized filter at configuration time. At runtime, you turn on the filter and apply parameters, and every query for that entity will include the filter.

Filters are used to define a predicate that will be applied to every entity in a DbContext, without a developer needing to remember to include it for every query. Common applications include:

 - Security
 - Multi-tenancy
 - Logical data partitioning
 - Soft deletes
 - Active/inactive records


## Configuration

The FilterInterceptor must be registered with Entity Framework, either through a DbConfiguration class:


```csharp
public class ExampleConfiguration : DbConfiguration
{
    public ExampleConfiguration()
    {
        AddInterceptor(new FilterInterceptor());
    }
}
```

Or through the OnModelCreating method:


```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    DbInterception.Add(new FilterInterceptor());
}
```

## Define Filters

### Single Entity

Filters are first defined, then configured. You define the filter against a single entity:


```csharp
modelBuilder.Entity<Listing>()
    .Filter("ActiveListings", c => c.Condition<ListingStatus>(
        listing => listing.Status == ListingStatus.Active));
```

### Set of Entities

Filters are also defined against a set of entities that match a type (interface or base class):


```csharp
modelBuilder.Conventions.Add(
    FilterConvention.Create<IAgencyEntity, int>("Agency", (e, agencyId) => e.AgencyId == agencyId);
```

### Enable Filters

Filters are then enabled and parameter values filled in on a DbContext basis:


```csharp
dbContext.EnableFilter("ActiveListings");
dbContext.EnableFilter("Agency")
    .SetParameter("agencyId", _userContext.CurrentUser.AgencyId);
```

### Disable Filters

Filters are disabled by default, and you can disable them selectively after enabling:


```csharp
dbContext.DisableFilter("ActiveListings");
```

The filter names must be unique, and filter parameter names are matched by the parameter name you supply to the filter definition's expression.

## Requirements

### Entity Framework Version

 - Entity Framework 6.

## Limitations

 - No access to context for complex joins
 - Collection properties not available

[Learn more](https://github.com/jbogard/EntityFramework.Filters)