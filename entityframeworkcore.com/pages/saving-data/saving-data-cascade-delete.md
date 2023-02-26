---
permaid: 1000244
Title: EF Core Cascade Delete - Learn How to Auto Delete Child Entities 
MetaDescription: Unlock the power of EF Core by using Cascade Delete to automatically delete child-related entities without any additional code. Learn how you can make sql server delete entities implicitly.
LastMod: 2023-02-24
tags: save delete
---

# EF Core Cascade Delete: Discover How to Auto Delete Child Entities

## Introduction

Cascade delete allows the deletion of a row to trigger the deletion of related rows automatically. 

 - EF Core covers a closely related concept and implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships. 
 - In Entity Framework Core, the `OnDelete` Fluent API method is used to specify the delete behavior for a dependent entity when the principal is deleted.

The OnDelete method takes a DeleteBehavior enum as a parameter:

 - **Cascade:** Child/dependent entity should be deleted
 - **Restrict:** Dependents are unaffected
 - **SetNull:** The foreign key values in dependent rows should update to NULL

Setting a foreign key value to null is not valid if the foreign key is not nullable. The following example set a foreign key field to null when the principal is deleted.

```csharp
using (var context = new MyContext())
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Invoice>()
            .HasOne(i => i.Customer)
            .WithMany(c => c.Invoices)
            .OnDelete(DeleteBehavior.SetNull+-*);
    }
}
```

The following example configures the relationship as required and specify that dependant rows are deleted when the principal is deleted.

```csharp
using (var context = new MyContext())
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Invoice>()
            .HasOne(i => i.Customer)
            .WithMany(c => c.Invoices)
            .IsRequired()
            .OnDelete(DeleteBehavior.Cascade);
    }
}
```

## Delete Behaviors

There are four delete behaviors. 

##### Optional Relationships (nullable foreign key)

It is possible to save a null foreign key value, which results in the following effects.

|Behavior Name    |Effect on dependent/child in memory    |Effect on dependent/child in database |
|:--------------- |:------------------------------------- |:------------------------------------ |
|Cascade          |Entities are deleted                   |Entities are deleted                  |
|ClientSetNull (Default)      |Foreign key properties are set to null| None                      |
|SetNull          |Foreign key properties are set to null |Foreign key properties are set to null|
|Restrict         |None                                   |None                                  |

##### Required Relationships (non-nullable foreign key) 

It is not possible to save a null foreign key value, which results in the following effects:

|Behavior Name    |Effect on dependent/child in memory    |Effect on dependent/child in database |
|:--------------- |:------------------------------------- |:------------------------------------ |
|Cascade (Default)   |Entities are deleted                |Entities are deleted                  |
|ClientSetNull    |SaveChanges throws                     |None                                  |
|SetNull          |SaveChanges throws                     |SaveChanges throws                    |
|Restrict         |None                                   |None                                  |
