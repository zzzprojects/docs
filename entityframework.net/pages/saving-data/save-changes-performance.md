---
PermaID: 1000051
Name: Save Changes Performance
---

# SaveChanges Performance

## How to Improve SaveChanges Performance? 

The DbContext.SaveChanges is a poor choice for BULK operations as far as performance is concerned. Once you get beyond a few thousand records, the SaveChanges method really starts to break down.

```csharp
using (var ctx = new CustomerContext())
{
    List<Customer> customers = new List<Customer>();
    
    for(int i = 0; i < 1000; i++)
	{
		list.Add(new Customer() { Name ="Customer_" + i});
	}
	
	context.Customers.AddRange(list);

    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/n3JMye)

### StackOverflow Related Questions

 - [Entity framework performance issue, saveChanges is very slow](https://stackoverflow.com/questions/21272763/entity-framework-performance-issue-savechanges-is-very-slow)
 - [Entity framework performance slow](https://stackoverflow.com/questions/40526018/entity-framework-performance-slow?rq=1)
 - [When should I call SaveChanges() when creating 1000's of Entity Framework objects?](https://stackoverflow.com/questions/1930982/when-should-i-call-savechanges-when-creating-1000s-of-entity-framework-object?rq=1)

## Answer

[Entity Framework Extensions](http://entityframework-extensions.net/) library adds the BulkSaveChanges extension method to the DbContext. It performs save operations 10 to 50 times faster. 

All changes made in the context are persisted in the database but way faster by reducing the number of database round-trip required!

BulkSaveChanges supports everything:

 - Association (One to One, One to Many, Many to Many, etc.)
 - Complex Type
 - Enum
 - Inheritance (TPC, TPH, TPT)
 - Navigation Property
 - Self-Hierarchy
 - Etc.


```csharp
using (var ctx = new CustomerContext())
{
    List<Customer> customers = new List<Customer>();
    
    for(int i = 0; i < 1000; i++)
	{
		list.Add(new Customer() { Name ="Customer_" + i});
	}
	
	context.Customers.AddRange(list);

    context.BulkSaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/ii773p)

[Learn more](http://entityframework-extensions.net/tutorial-bulk-savechanges)

## Improve BulkSaveChanges

BulkSaveChanges is already very fast, but you can make it even faster by simply turning off the **EntityFrameworkPropagation** option.

```csharp
EntityFrameworkManager.DefaultEntityFrameworkPropagationValue = false;
```
[Try it online](https://dotnetfiddle.net/QSAcrl)

When turning off this option, the library does no longer use the methods from Entity Framework but internal methods from our [Entity Framework Extensions](http://entityframework-extensions.net/) library.

[Learn more](http://entityframework-extensions.net/improve-bulk-savechanges)