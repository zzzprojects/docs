---
PermaID: 1000082
Name: Check Object Existence
---

# Check Object Existence

## How to check if an object exists in the database?

What is the best way performance wise to check if the object exists in the database. 

### StackOverflow Related Questions

 - [Best way to check if object exists in Entity Framework?](https://stackoverflow.com/questions/1802286/best-way-to-check-if-object-exists-in-entity-framework)


## Answer

For IEnumerable<T>, using **Any()** method is the fastest way to check if the object exists in the database. It only has to look at one iteration and will return as soon as it finds a match.


```csharp
using (var context = new EntityContext())
{
    if(context.Customers.Any(c => c.CustomerID == 1))
    {
        //match
    }  
}
```

[Try it online](https://dotnetfiddle.net/zSY8BL)