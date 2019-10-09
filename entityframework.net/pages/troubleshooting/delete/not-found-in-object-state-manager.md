---
PermaID: 1000160
Name: Not found in ObjectStateManager
---

# Not Found in ObjectStateManager

## Exception: The object cannot be deleted because it was not found in the ObjectStateManager

When an entity is fetched and deleted using different DbContext object, then it throws an exception **The object cannot be deleted because it was not found in the ObjectStateManager.**

```csharp
Customer deletedCustomer = null;
using (var context = new EntityContext())
{
    deletedCustomer = context.Customers.Where(c => c.CustomerID == 3).FirstOrDefault();
}

using (var context = new EntityContext())
{
    context.Customers.Remove(deletedCustomer);
    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/bgqpUy)

In the above example, the customer entity is fetched in one DbContext instance, and then it is removed/deleted from the database in another DbContext instance.  

### StackOverflow Related Questions

 - [The object cannot be deleted because it was not found in the ObjectStateManager](https://stackoverflow.com/questions/7791149/the-object-cannot-be-deleted-because-it-was-not-found-in-the-objectstatemanager)

## Solution

To avoid the above exception, make sure that either entity is fetched and deleted in the same DbContext instance or an entity is attached to the context before removing it.
 
### Use Same DbContext Instance

```csharp
using (var context = new CustomerContext())
{
    var deletedCustomer = context.Customers.Where(c => c.CustomerID == 3).FirstOrDefault();
    context.Customers.Remove(deletedCustomer);
    context.SaveChanges();
}
```

[Try it online](https://dotnetfiddle.net/c8IVLD)

### Attach Before Deleting

```csharp
Customer deletedCustomer = null;
using (var context = new EntityContext())
{
    deletedCustomer = context.Customers.Where(c => c.CustomerID == 3).FirstOrDefault();
}

using (var context = new EntityContext())
{
    context.Customers.Attach(deletedCustomer);
    context.Customers.Remove(deletedCustomer);
    context.SaveChanges();
}
```

[Try it online](https://dotnetfiddle.net/mexawY)