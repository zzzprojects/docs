---
Name: Overview
---

# Overview

## Description

**EntityFramework Dynamic Filters** is a library that creates global and scoped filters for Entity Framework queries.

The filters are automatically applied to every query and can be used to support use cases such as Multi-Tenancy, Soft Deletes, Active/Inactive, etc.

## Installing & Upgrading
Download the <a href="/download">NuGet Package</a>

## Requirements

### Entity Framework Version

- Entity Framework 6.x

## How It Works?
The library can be easily installed through <a href="/installing">NuGet</a> which will add extension methods automatically to DbContext and DbModelBuilder classes to access **Dynamic Filters**.

- Filters are defined in DbContext.OnModelCreating().
- Filters can be created using boolean LINQ expressions.
- It also supports the Contains() operator to define filters.

## Enabling and Disabling Filters

Filters can be easily disabled by using the DbContext.DisableFilter extension method. To disable a filter globally, use DbModelBuilder.DisableFilterGlobally method after it is created in OnModelCreating


```csharp

context.DisableFilter("IsDeleted");

//Disable a filter globally
modelBuilder.DisableFilterGlobally("IsDeleted");

```

You can also enable/disable all filters at once within a context. 


```csharp

context.DisableAllFilters();
context.EnableAllFilters();

```

## Different Ways To Define Filters

### Specific Value

Filters can be defined on a specific entity class or an interface by providing a specific value, e.g. an **IsDeleted** filter created on an **ISoftDelete** interface which will automatically filter those entities by applying the condition "IsDeleted==false".


```csharp

modelBuilder.Filter("IsDeleted", (ISoftDelete d) => d.IsDeleted, false);

```

This filter will apply to all classes that implement ISoftDelete.

### Delegate Expressions

Filter values can also be provided via a delegate/expression instead of a specific value which allows you to change the parameter value dynamically. For example, a filter can be created on the UserID and provided per HTTP request.


```csharp

modelBuilder.Filter("Notes_CurrentUser", (Note n) => 
			n.PersonID, () => GetPersonIDFromPrincipal(Thread.CurrentPrincipal));

```

Each time the query is executed, this delegate will be evaluated to obtain the **PersonID** associated with each request.

### Parameter Expressions

Parameter delegate expressions can be specified as either a **Func < object >** or a **Func<DbContext, object>**.


```csharp

// Specified as Func<object>
modelBuilder.Filter("Notes_CurrentUser", (Note n) => 
			n.PersonID, () => GetPersonIDFromPrincipal(Thread.CurrentPrincipal));

// Specified as Func<DbContext, object>
modelBuilder.Filter("Notes_CurrentUser", (Note n) => 
			n.PersonID, (MyContext ctx) => ctx.CurrentPersonID);

```

In the latest declaration, the parameter value is set to the value of the **CurrentPersonID** property in the current DbContext instance.

### LINQ Filters

Filters can also be created using linq conditions and with multiple parameters.

The following command creates a filter that limits BlogEntry records by **AccountID** and an **IsDeleted** flag.


```csharp

modelBuilder.Filter("BlogEntryFilter", 
			(BlogEntry b, Guid accountID, bool isDeleted) => 
			(b.AccountID == accountID) && (b.IsDeleted == isDeleted), () => 
			GetPersonIDFromPrincipal(Thread.CurrentPrincipal), () => false);

```

A parameter is created for each condition with parameter names "accountID" and "isDeleted".

The LINQ syntax supports the Contains() operator on Enumerable<T> to generate SQL "in" clauses:


```csharp

var values = new List<int> { 1, 2, 3, 4, 5 };
modelBuilder.Filter("ContainsTest", (BlogEntry b, List<int> valueList) => 
				valueList.Contains(b.IntValue.Value), () => values);

```

### Changing Parameter Values

Filter parameter values can be changed easily within a DbContext instance, and these changes will be scoped to that instance only which will not affect other DbContext instances.

Parameter values can be set to a specific value or delegate expressions (**Func < object >** or **Func<DbContext, object>**).


```csharp

//Change Soft Delete filter to return only deleted records
context.SetFilterScopedParameterValue("IsDeleted", true);

//For multiple parameters, you must specify the name of the parameter
context.SetFilterScopedParameterValue("BlogEntryFilter", "accountID", 12345);

```

Global parameter values can also be changed using the SetFilterGlobalParameterValue extension methods.

## Conditionally Enabling Filters

Filters can also be enabled conditionally and you will even need to define those conditions along with your filter definition.


```csharp

modelBuilder.Filter("BlogEntryFilter", (BlogEntry b, Guid accountID) => (b.AccountID == accountID), 
                    () => GetPersonIDFromPrincipal(Thread.CurrentPrincipal));
modelBuilder.EnableFilter("BlogEntryFilter", () => !UserIsAdmin(Thread.CurrentPrincipal));

```

In this example, a filter is defined on BlogEntry records to restrict only the current AccountID. But the filter will only be enabled if the user is not an Admin user.
