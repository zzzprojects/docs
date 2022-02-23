---
Name: Initial Post
---

## Description

Source: https://refactorthis.wordpress.com/2012/12/11/introducing-graphdiff-for-entity-framework-code-first-allowing-automated-updates-of-a-graph-of-detached-entities/

Looking for a complete solution for automatically updating a graph of entities using the Entity Framework? Read On!

Hi, As usual I have neglected this blog as of late. It is getting harder and harder to find time to put some notes up here. But hopefully today I have something very interesting to make up for it.

So today I’m finally going to post something that we have actually been using live on our production code for quite some time, and the good news is that it is working beautifully. I’m introducing GraphDiff – an extension method allowing for the automatic update of a detached graph using Entity Framework code first. (Edit: code has been rewritten to handle multiple new features, as such no guarantee can be given on its production-ready usage, but I’m continuing to work on it to make sure it is relatively bug-free)

Working with detached graphs of entities we quite often found that it was cumbersome to use the Entity Framework to manually map all of the changes from an aggregate root to the database. By aggregate root I mean a bunch of models which are handled as one unit when updating/adding/deleting.

Below I will describe my proposed solution to this problem of automatically updating a detached graph consisting of multiple add/delete/update changes at any point in the graph. This should work for all sorts of graphs, and allows for updating entities with associated collections and single entities.

I find its always clearer with some code so lets try an example:

Say you have a Company which has many Contacts. A contact is not defined on its own and is a One-To-Many (with required parent) record of a Company. i.e. The company is the Aggregate Root. Assume you have a detached Company graph with its Contacts attached and want to reflect the state of this graph in the database.

At present using the Entity Framework you will need to perform the updates of the contacts manually, check if each contact is new and add, check if updated and edit, check if removed then delete it from the database. Once you have to do this for a few different aggregates in a large system you start to realize there must be a better, more generic way.

Well good news is that after a few refactorings I’ve found a nice solution to this problem. The proposed extension method below handles the whole diff for you in a nice convenient package.

```csharp
using (var context = new TestDbContext())
{
    // Update the company and state that the company 'owns' the collection Contacts.
    context.UpdateGraph(company, map => map
        .OwnedCollection(p => p.Contacts)
    );
 
    context.SaveChanges();
}
```

Using the above code a diff will be run between the provided company graph and one retrieved from the database. The bounds of the graph are defined by the mapping which above is the company entity itself and its child Contacts. Only entities within these bounds will be included in the database diff.

The retrieval code makes use of the provided mapping configuration to get all data needed in one query at the start of the process, thus making the process quite efficient.

From this diff the algorithm will add/update/delete depending on what action needs to be performed and commit all of these changes in one batch at the end of the algorithm.

There are 2 different scenarios that this extension method must cater for. One is the situation above where you have a One-To-Many or One-To-One where the right hand side of the relationship is owned by the parent. This is defined within the mapping as an OwnedCollection/OwnedEntity.

The other scenario is that you have a Many-To-Many, or a One-To-Many and the related record exists in its own right as it not a defined part of the aggregate you are updating. That scenario is defined as an AssociatedCollection/AssociatedEntity.

The mapping configuration can handle many complex scenarios with ease. For example:

I have a company aggregate which is made of address objects and contact objects. On the contact model there is a list of accepted advertising materials (List) which are managed elsewhere in the system and not a part of the company aggregate. The list of advertisingOptions that are associated with the contact still needs to be updated however when managing the company as an aggregate.

```csharp
using (var context = new TestDbContext())
{
    // Update the company and state that the company 'owns' the collection Contacts.
    context.UpdateGraph(company, map => map
        .OwnedCollection(p => p.Contacts, with => with
            .AssociatedCollection(p => p.AdvertisementOptions))
        .OwnedCollection(p => p.Addresses)
    );
 
    context.SaveChanges();
}
```

The associated collection line tells the update method that the actual entities that are AdvertisementOptions should not be marked as changed (even if the objects provided are different from the database values. We do however want to update the relation so that it reflects the fact that the contact has the provided AdvertisementOptions and this is performed during the update.

If you would like to use this code please leave all copyright marks as they are. The code has been put up on https://github.com/zzzprojects/GraphDiff

Hope this helps you as it did our team. Please note that there are a few things that can still be improved and if you have any suggestions please let me know.
