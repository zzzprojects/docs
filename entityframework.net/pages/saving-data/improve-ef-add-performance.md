---
PermaID: 1000072
Name: Add Performance
---

# Improve EF Add Performance

## How to Improve Entity Framework Add Performance?

When you overuse the Add() method for multiple entities, your application suffers from performance issues.


```csharp
using (var ctx = new CustomerContext())
{
    foreach(var line in lines)
    {
        var customer = new Customer();
        // ...code...
        ctx.Customers.Add(customer);
    }
    
    ctx.SaveChanges();
}
```

### StackOverflow Related Questions

 - [WebAPI EF update 30,000 rows of data is very slow](https://stackoverflow.com/questions/38925835/webapi-ef-update-30-000-rows-of-data-is-very-slow/38938259)
 - [Performance of Entity Framework [closed]](https://stackoverflow.com/questions/37204130/performance-of-entity-framework/37214956)

## Answer

### Is Entity Framework Add Really Slow?

In fact, the **Add** method is not slow at all. Adding an entity to a list cannot be that slow. It's the DetectChanges method invoked inside the Add method which is insanely slow!

Using the **Add** method in a loop is usually a poor practice which impacts your application performance severely when poorly used.

 - **USE** AddRange over Add (**Recommended**)
 - **SET** AutoDetectChanges to false
 - **SPLIT** SaveChanges in multiple batches

### Use AddRange over Add (Recommended)

When adding multiple entities, you should always use Entity Framework AddRange once with a list instead of calling multiple time the Add method.

#### Why?

 - The Add method DetectChanges after every records added.
 - The AddRange method DetectChanges after all records are added.

#### Performance Comparisons

|Operations	|100 Entities	|1,000 Entities	|10,000 Entities|
|:--------- |:------------- |:------------- |:--------------|
|Add	    |15 ms	        |1,050 ms	    |105,000 ms     |
|AddRange	|1 ms	        |10 ms	        |120 ms         |

***Note:***
 - **: SaveChanges time not included*
 - ***: Entity with two relations*

#### How?

 1. CREATE a list
 2. ADD entity to the list
 3. USE AddRange with the list
 4. SaveChanges
 5. Done!


```csharp
using (var context = new EntityContext())
{
    // 1. CREATE a list
    List<Customer>  list = new List<Customer>();
    
    for(int i = 0; i < 2000; i++)
    {
        var customer = new Customer();
        // ...code...
    
        // 2. ADD entity to the list
    	list.Add(customer);
    }
	
    // 3. USE AddRange with the list		
    context.Customers.AddRange(list);
	
    // 4. SaveChanges
    ctx.SaveChanges();
    
    // 5. Done!
}
```
[Try it online](https://dotnetfiddle.net/gPXbQ8)

### SET AutoDetectChanges to false

When adding multiple entities, if you cannot use AddRange, set Entity Framework AutoDetectChanges to false

#### Why?

 - The Add method DetectChanges after every records added.

By disabling AutoDetectChanges, the DetectChanges method will only be invoked when you do it.

#### Performance Comparisons

|Operations	    |100 Entities	|1,000 Entities	|10,000 Entities|
|:---------     |:------------- |:------------- |:--------------|
|True (Default)	|15 ms	        |1,050 ms	    |105,000 ms     |
|False	        |1 ms           |14 ms	        |180 ms         |

***Note:***
 - **: SaveChanges time not included*
 - ***: Entity with two relations*

#### How?

 1. SET AutoDetectChangesEnabled = false
 2. CALL DetectChanges before SaveChanges
 3. SaveChanges
 4. Done!

```csharp
using (var context = new EntityContext())
{
    // 1. SET AutoDetectChangesEnabled = false
    context.Configuration.AutoDetectChangesEnabled = false;
     
    List<Customer>  list = new List<Customer>();

    for(int i = 0; i < 2000; i++)
    {
        var customer = new Customer();
        // ...code...
    
    	list.Add(customer);
    }
    
    context.Customers.AddRange(list);
    
    // 2. CALL DetectChanges before SaveChanges
    context.ChangeTracker.DetectChanges();
    
    // 3. SaveChanges
    context.SaveChanges();
    
    // 4. Done!
}
```
[Try it online](https://dotnetfiddle.net/RFsvB5)

### SPLIT SaveChanges into multiple batches

This solution is not recommended. When adding multiple entities, split entities with a batch size in multiple different contexts.

#### Why?

More tracking entities your context contains, slower the DetectChanges method is! So by reducing the number of entities by context, you improve the performance.

#### Performance Comparisons

|Operations |100 Entities	|1,000 Entities	|10,000 Entities|
|:--------- |:------------- |:------------- |:--------------|
|Unlimited	|15 ms	        |1,050 ms	    |105,000 ms     |
|10	        |3 ms	        |40 ms	        |350 ms         |
|100	    |15 ms	        |125 ms	        |1,200 ms       |
|1,000	    |15 ms	        |1,050 ms	    |10,200 ms      |

***Note:***
 - **: SaveChanges time not included*
 - ***: Entity with two relations*

#### How

 1. CREATE a batchSize variable
 2. CALL SaveChanges before creating a new batch
 3. CALL SaveChanges
 4. Done!

```csharp
// 1. CREATE a batchSize variable
int batchSize = 400;
		
var context = new EntityContext();

for(int i = 0; i <= 2000; i++)
{
    // 2. CALL SaveChanges before creating a new batch
    if (i != 0 && i%batchSize == 0)
    {
    	context.SaveChanges();
    	context = new EntityContext();
    }
    
    var customer = new Customer();
    // ...code...
    
    context.Customers.Add(customer);
}

// 3. CALL SaveChanges
context.SaveChanges();

// 4. Done!
```
[Try it online](https://dotnetfiddle.net/ch7vyd)