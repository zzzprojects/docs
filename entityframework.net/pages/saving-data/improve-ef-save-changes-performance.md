---
PermaID: 1000068
Title: Entity Framework SaveChanges - Learn How to Improve Save Performance
MetaDescription: Unlock the power of Entity Framework by understanding how to improve the SaveChanges method performance. Learn why the method is slow and how you can overcome this limitation.
LastMod: 2025-06-18
Tags: saving performance
---

# Entity Framework SaveChanges: Discover How to Improve Save Performance

## How to Improve SaveChanges Performance? 

The DbContext.SaveChanges is a poor choice for BULK operations as far as performance is concerned. Once you get beyond a few thousand records, the SaveChanges method really starts to break down.

```csharp
using (var ctx = new CustomerContext())
{
    List<Customer> customers = new List<Customer>();
    
    foreach(var line in lines)
    {
        var customer = new Customer();
        // ...code...
        
        customers.Add(customer);
    }
    
    ctx.Customers.AddRange(customers);

    ctx.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/0Bbdla)

### StackOverflow Related Questions

 - [Entity framework performance issue, saveChanges is very slow](https://stackoverflow.com/questions/21272763/entity-framework-performance-issue-savechanges-is-very-slow)
 - [Entity framework performance slow](https://stackoverflow.com/questions/40526018/entity-framework-performance-slow?rq=1)
 - [When should I call SaveChanges() when creating 1000's of Entity Framework objects?](https://stackoverflow.com/questions/1930982/when-should-i-call-savechanges-when-creating-1000s-of-entity-framework-object?rq=1)

## Answer

[Entity Framework Extensions](https://entityframework-extensions.net/) library adds the BulkSaveChanges extension method to the DbContext. It performs save operations 10 to 50 times faster. 

All changes made in the context are persisted in the database but way faster by reducing the number of database round-trips required!

BulkSaveChanges supports everything:

 - Association (One to One, One to Many, Many to Many, etc.)
 - Complex Types
 - Enum
 - Inheritance (TPC, TPH, TPT)
 - Navigation Property
 - Self-Hierarchy
 - Etc.

```csharp
using (var ctx = new CustomerContext())
{
    List<Customer> customers = new List<Customer>();
    
    foreach(var line in lines)
    {
        var customer = new Customer();
        // ...code...
        
        customers.Add(customer);
    }
    
    ctx.Customers.AddRange(customers);

    ctx.BulkSaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/jNqaMF)

[Learn more](https://entityframework-extensions.net/tutorial-bulk-savechanges)

## Improve BulkSaveChanges

BulkSaveChanges is already very fast, but you can make it even faster by simply turning off the **EntityFrameworkPropagation** option.

```csharp
EntityFrameworkManager.DefaultEntityFrameworkPropagationValue = false;
```
[Try it online](https://dotnetfiddle.net/NxeEId)

When turning off this option, the library no longer uses the methods from Entity Framework but internal methods from our [Entity Framework Extensions](https://entityframework-extensions.net/) library.

[Learn more](https://entityframework-extensions.net/improve-bulk-savechanges)