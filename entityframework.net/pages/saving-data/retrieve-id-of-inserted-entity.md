---
PermaID: 1000083
Title: Entity Framework Retrieve Inserted Id - Learn about IDENTITY
MetaDescription: Unlock the power of Entity Framework by understanding how to retrieve the inserted id after you insert an entity. Learn how an identity column works.
LastMod: 2023-02-28
Tags: saving identity
---

# Entity Framework Retrieve Inserted Id: Discover about IDENTITY

## How can I get Id of inserted entity? 

I have a problem with Entity Framework in Asp.net. I want to get the Id value whenever I add an object to database. How can I do this?

### StackOverflow Related Questions

 - [How can I get Id of inserted entity in Entity framework?](https://stackoverflow.com/questions/5212751/how-can-i-get-id-of-inserted-entity-in-entity-framework)
 - [Get Record ID in Entity Framework after insert](https://stackoverflow.com/questions/16954767/get-record-id-in-entity-framework-after-insert)

## Answer

It is pretty easy. If you are using DB generated Ids (like `IDENTITY` in MS SQL) you just need to add entity to the contexct and call SaveChanges on that context. Id will be automatically filled for you.


```csharp
using (var context = new EntityContext())
{
	var customer = new Customer()
	{
		Name = "John"
	};

	context.Customers.Add(customer);
	context.SaveChanges();
		
	int id = customer.CustomerID;
}
```
[Try it online](https://dotnetfiddle.net/YsMZR6)

Entity framework by default follows each `INSERT` with SELECT `SCOPE_IDENTITY()` when auto-generated Ids are used.