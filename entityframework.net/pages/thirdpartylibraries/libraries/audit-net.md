---
PermaID: 1000012
Name: Audit.NET
---

# Audit.NET

## Definition

**Audit.NET** is an extensible framework to audit executing operations in .NET applications.

It generates an audit log with evidence for reconstruction and examination of activities that have affected specific operations or procedures.

## AuditScope

The **AuditScope** is the central object of this framework. It encapsulates an audit event, controlling its lifecycle. The Audit Event is an extensible information container of an audited operation.

Create an Audit Scope by calling the static AuditScope.Create method.


```csharp
Order order = Db.GetOrder(orderId);
using (AuditScope.Create("Order:Update", () => order))
{
    order.Status = -1;
    order.OrderItems = null;
    order = Db.OrderUpdate(order);
}
```

 - The first parameter of the Create method is an event type name intended to identify and group the events. 
 - The second is the delegate to obtain the object to track (target object), and this object is passed as a `Func<object>` to allow the library inspect the value at the beginning and at the disposal of the scope. 
 - It is not mandatory to supply a target object, pass `null` when you don't want to track a specific object.

## Discard Option

The AuditScope object has a Discard() method to allow the user to discard an event under specific condition.

For example, if you want to avoid saving the audit event when an exception is thrown:


```csharp
using (var scope = AuditScope.Create("SomeEvent", () => someTarget))
{
    try
    {
        //some operation
        Critical.Operation();
    }
    catch (Exception ex)
    {
        //If an exception is thown, discard the audit event
        scope.Discard();
    }
}
```

## Requirements

### Entity Framework Version

 - Entity Framework Core
 - Entity Framework 6

[Learn more](https://github.com/thepirat000/Audit.NET)