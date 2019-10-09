---
PermaID: 1000052
Name: Preserve Insert Order
---

# Preserve Insert Order

## How to Preserve Insert Order? 

The Entity Framework doesn't insert the records in the following order.


```csharp
dbContext.SomeTables1.Add(object1)
dbContext.SomeTables2.AddRange(objectArray2)
dbContext.SomeTables3.AddRange(objectArray3)
dbContext.SaveChanges();
```
But it inserts them in a random order. To insert them in the same order, I have to call SaveChanges() after each addition. This is not an efficient solution and in my case, it is taking 10 seconds to do all my inserts, while the random order with one save takes around 3 seconds.

### StackOverflow Related Questions

 - [What logic determines the insert order of Entity Framework 6](https://stackoverflow.com/questions/34795577/what-logic-determines-the-insert-order-of-entity-framework-6/)
 - [Insertion order of multiple records in Entity Framework](https://stackoverflow.com/questions/11521057/insertion-order-of-multiple-records-in-entity-framework/)

## Answer

There is no way you can specify a save order in EF6 or EF7. The issue is not resolved in EF7 since this is not an issue.

 - The order will be the same if the predecessor are the same (which will likely rarely happen).
 - When you call `SaveChanges`, all entities are ordered from an internal order in the method `ProduceDynamicCommands` then sorted again by the method `TryTopologicalSort` which loop to add command with no predecessor left (if you add A and B and A depend of B, then B will be inserted before A)

You are left to insert by batch addition.

Since it take you 3 seconds to perform your insert, I will assume you have thousands of entities and performing bulk insert may improve your performance to reduce the 10 seconds to less then maybe the intial 3 seconds!

Here is a library called [Entity Framework Extensions (EFE)](http://entityframework-extensions.net/) I can recommend. It is PAID but support everything:

 - Association (One to One, One to Many, Many to Many, etc.)
 - Complex Type
 - Enum
 - Inheritance (TPC, TPH, TPT)
 - Navigation Property
 - Self-Hierarchy
 - Etc.

[Learn more](http://entityframework-extensions.net/overview)