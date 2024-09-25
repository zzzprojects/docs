---
Title: 3 Effective Ways to Use the Dapper Plus Context 
MetaDescription: Learn how to use Dapper Plus Context with our comprehensive guide on 'Global Context', 'Instance Context', and 'Inheritance Context'.
LastMod: 2024-09-25
---

# 3 Effective Ways to Use the Dapper Plus Context

The `DapperPlusContext` is the class where all the magic happens. All [Bulk Extension](bulk-extensions-methods) and [Single Extension](single-extensions-methods) methods execute use an instance of a `DapperPlusContext`. Even when you utilize methods through a connection or transaction, you are employing what we call the global context, which is simply a new instance of the `DapperPlusContext` stored in a static variable in the [DapperPlusManager](/dapper-plus-manager):

```csharp
public class DapperPlusManager
{
    public static DapperPlusContext DefaultContext { get; set; } = new DapperPlusContext();
    
    // ...code...
}
```

But what exactly is a `DapperPlusContext`?

To put it simply, a `DapperPlusContext` is a class that contains all your mapping and options information, which will be utilized whenever you call a method from it.

There are three different ways to use the DapperPlusContext:

- **Global Context**
- **Instance Context**
- **Inheritance Context**

Each method has its pros and cons; choosing the right one will depend on your specific needs.

## Global Context

The global context mapping is the most commonly used one. This mapping is applied whenever you execute an action from the `connection` or `transaction`.

However, there is one simple rule to follow: **This is global, so ensure wherever you map, it's called only once.**

**A common mistake** people make is performing a mapping for the global context inside a method. This can lead to potential concurrency issues, as one call of the method might be at the step of re-creating the mapping while another call might be using the mapping.

As we noted in our introduction, the global context mapping is simply an instance of the `DapperPlusContext` stored in a variable in the [DapperPlusManager](/dapper-plus-manager):

```csharp
public class DapperPlusManager
{
    public static DapperPlusContext DefaultContext { get; set; } = new DapperPlusContext();
    
    // ...code...
}
```

You can configure [mapping](/mapping) and [options](/options) directly through the [DapperPlusManager](/dapper-plus-manager) or `DefaultContext`:

```csharp
// commonly used method
DapperPlusManager.Entity<Product>().Identity(x => x.ProductID);

// alternative method using DefaultContext
DapperPlusManager.DefaultContext.Entity<Product>().Identity(x => x.ProductID);
```

## Instance Context

The instance mapping allows you to map your entities specifically for the scope of a `DapperPlusContext` instance. You then call [Bulk Extension Methods](/bulk-extensions-methods) directly from this instance.

This approach is particularly useful inside a method when the context of the mapping might change depending on certain parameters. In the following example, you will see that we've added a parameter to specify whether our mapping should keep the identity value provided and the column names to insert:

```csharp
public static void CustomInsert(IEnumerable<Product> products, bool keepIdentityValue, List<string> mappingColumnNames)
{
	var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	var context = new DapperPlusContext(connection);

	var mapping = context.Entity<Product>().Identity(x => x.ProductID);
	
	mappingColumnNames.ForEach(x => mapping.Map(x));

	if(keepIdentityValue)
	{
		mapping.UseBulkOptions(options => options.InsertKeepIdentity = true);
	}

	context.BulkInsert(products);
}
```

[Online Example](https://dotnetfiddle.net/JODTb7)

The instance context is the most flexible way to use our library, especially when you cannot determine the mapping and options in advance.

## Inheritance Context

Inheritance mapping allows you to define how entities are mapped within the scope of a context instance that inherits from `DapperPlusContext`. This method is particularly useful when you need different mappings based on parameters passed in your constructors, or when you wish to maintain these mappings in your own variable.

Here's an example where a specialized `ProductContext` is set up to handle only products:

```csharp
public class ProductContext : DapperPlusContext
{
    public ProductContext() : base(new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
    {
        this.Entity<Product>().Identity(x => x.ProductID, true);
    }
}

var productContext = new ProductContext();
productContext.BulkInsert(products);
```

[Online Example](https://dotnetfiddle.net/qGVnl2)

This inheritance context approach allows for robust, context-specific configurations that are encapsulated within their own classes, thereby enhancing modularity and readability of your data access layer.

## Conclusion
In this article, we explored three different ways to use the Dapper Plus context.

It's important not to limit yourself to only the global context mapping, as it does not cover all scenarios.

Typically, people new to our library start by using the global context, but they often encounter scenarios where using an instance context or an inheritance context might be a more suitable solution for their specific needs.