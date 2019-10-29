---
PermaID: 1000162
Name: Concurrency
---

# Concurrency

## Exception: Store update, insert, or delete statement affected an unexpected number of rows (0)

A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates or deletes the same entity's data before the first user's change is written to the database. 

Another case for this exception is when a new object is created and and it's state is set to modified the EntityState.Modified.

```csharp
using (var context = new EntityContext())
{
    var customer = new Customer();
    // ...code...

    context.Entry(customer).State = EntityState.Modified;
    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/qsBxKA)

### StackOverflow Related Questions

 - [Entity Framework: Store update, insert, or delete statement affected an unexpected number of rows (0).](https://stackoverflow.com/questions/1836173/entity-framework-store-update-insert-or-delete-statement-affected-an-unexpec)
 - [Solution for: Store update, insert, or delete statement affected an unexpected number of rows (0)](https://stackoverflow.com/questions/6819813/solution-for-store-update-insert-or-delete-statement-affected-an-unexpected-n)

## Solution

This exception can be used to warn another user that record has been modified and this is the usual way of handling concurrency conflict.

But if there is a requirement like not to show the warning in case of concurrency conflict and handle it with in the code itself.  You can do it too and the idea here is getting the current database value and setting them as the original values for the entity.


```csharp
using (var context = new EntityContext())
{
    // ...code...
    try
    {
        context.SaveChanges();
    }
    catch (DbUpdateConcurrencyException ex)
    {
        var value = ex.Entries.Single();
        value.OriginalValues.SetValues(value.GetDatabaseValues());
        context.SaveChanges();
    }
}
```
Another solution to handle this exception is to set the state of the entity to **Added** instead of **Modified**.


```csharp
using (var context = new CustomerContext())
{
    var customer = new Customer();
    // ...code...

    context.Entry(customer).State = EntityState.Added;
    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/eUYel4)