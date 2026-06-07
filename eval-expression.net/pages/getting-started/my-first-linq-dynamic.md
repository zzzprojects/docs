---
Title: Execute LINQ Dynamic Expression with List and EF Core
MetaDescription: Learn how to use LINQ Dynamic query to execute methods such as Select, OrderBy, or Where clause dynamically.
LastMod: 2026-06-07
---

# My First LINQ Dynamic with C# Eval Expression

LINQ is an incredibly powerful way to query data. It allows developers to write code that is both expressive and easy to read. However, one thing that is missing is the ability to use LINQ with dynamic expressions.

Indeed, you can use LINQ for most of your queries, but what should you do if you want to allow your users to dynamically filter a list for a report? This is where using LINQ Dynamic through the C# Eval Expression library starts to shine.

The best part? All LINQ Dynamic features are completely free to use through our library.

You can use LINQ Dynamic methods with both `IEnumerable` and `IQueryable`. This also means you can use them with ORMs, such as Entity Framework Core and EF6.

In this tutorial, you will learn how to use our library to:

- [Use LINQ Dynamic with the WhereDynamic method](#use-linq-dynamic-with-the-wheredynamic-method)
- [Use LINQ Dynamic with the WhereDynamic method and variables](#use-linq-dynamic-with-the-wheredynamic-method-and-variables)
- [Use LINQ Dynamic with the Execute method](#use-linq-dynamic-with-the-execute-method)
- [Use LINQ Dynamic with other methods](#use-linq-dynamic-with-other-methods)

An alternative solution is to use our other popular [System.Linq.Dynamic](https://dynamic-linq.net/) library.

## Use LINQ Dynamic with the WhereDynamic Method

The `Where` method cannot directly accept a dynamic expression. However, the C# Eval Expression library lets you easily achieve this behavior with the `WhereDynamic` method.

Under the hood, our library creates the lambda expression and then calls the standard LINQ `Where` method. In other words, our library does not mimic or interpret the expression itself, it actually generates the lambda expression and uses the real LINQ `Where` method.

To use our method, you must first add the `using System.Linq;` directive to access the LINQ Dynamic extension methods.

In this example, we assume a typical scenario where a developer uses [EF Core](https://www.learnentityframeworkcore.com/) and needs to filter a customer list based on a custom filter entered by an end user. We will filter customers in the database and return only active customers who have logged in within the last month.

```csharp
// @nuget: Z.Expressions.Eval
using System.Linq;
using Z.Expressions;

var customers = context.Customers.WhereDynamic(x => "x.Status == Status && x.LastLogon >= DateTime.Now.AddMonths(-1)").ToList();

// ...or...

var customers = context.Customers.WhereDynamic("x => x.Status == 0 && x.LastLogon >= DateTime.Now.AddMonths(-1)").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/b5EmTM' %}

As shown in the example, you can specify the lambda expression parameter in two different ways:

* Outside the expression: `x => "expression"`
* Inside the expression: `"x => expression"`

There is no recommended approach, as both options are fully supported. Use whichever is the easiest and most readable for your scenario.


## Use LINQ Dynamic with the WhereDynamic Method and Variables

In the previous example, we already saw how powerful the `WhereDynamic` method can be. However, in real-world scenarios, you will often need to use variables as part of your dynamic expressions.

For example, you might expose predefined variables to help users create filters more easily, such as status values:

* `IsActive`
* `IsDeleted`
* `IsSuspended`

Or date-related variables to simplify expressions:

* `LastWeek`
* `LastMonth`
* `LastYear`

In this example, we will use the same filter as before (return only active customers who have logged in within the last month), but this time we will use variables instead of hardcoded values.

```csharp
// @nuget: Z.Expressions.Eval
using System.Linq;
using Z.Expressions;

// Passing parameters with an Anonymous Type using named members
{
	var customers = context.Customers.WhereDynamic(x => "x.Status == IsActive && x.LastLogon >= LastMonth", new {
		IsActive = CustomerStatus.IsActive,
		IsDeleted = CustomerStatus.IsDeleted,
		IsSuspended = CustomerStatus.IsSuspended,
		LastWeek = DateTime.Now.AddDays(-7),
		LastMonth = DateTime.Now.AddMonths(-1),
		LastYear = DateTime.Now.AddYears(-1)
	}).ToList();
}

// Passing parameters with a Dictionary and using key names
{
	var environmentVariables = new Dictionary<string, object>();

	// ADD Status variables
	environmentVariables.Add("IsActive", CustomerStatus.IsActive);
	environmentVariables.Add("IsDeleted", CustomerStatus.IsDeleted);
	environmentVariables.Add("IsSuspended", CustomerStatus.IsSuspended);

	// ADD DateTime variable
	environmentVariables.Add("LastWeek", DateTime.Now.AddDays(-7));
	environmentVariables.Add("LastMonth", DateTime.Now.AddMonths(-1));
	environmentVariables.Add("LastYear", DateTime.Now.AddYears(-1));			

	var customers = context.Customers.WhereDynamic(x => "x.Status == IsActive && x.LastLogon >= LastMonth", environmentVariables).ToList();
}

// Passing parameters using an Expando Object and using member names
// NOTE: When only using 1 parameter, you can directly use member names of the Expando Object
{
	dynamic expandoObject = new ExpandoObject();
	expandoObject.IsActive = CustomerStatus.IsActive;
	expandoObject.IsDeleted = CustomerStatus.IsDeleted;
	expandoObject.IsSuspended = CustomerStatus.IsSuspended;
	expandoObject.LastWeek = DateTime.Now.AddDays(-7);
	expandoObject.LastMonth = DateTime.Now.AddMonths(-1);
	expandoObject.LastYear = DateTime.Now.AddYears(-1);

	var customers = context.Customers.WhereDynamic(x => "x.Status == IsActive && x.LastLogon >= LastMonth", (object)expandoObject).ToList();
}

// Passing parameters with a class instance and using class members
{
	var customers = context.Customers.WhereDynamic(x => "x.Status == IsActive && x.LastLogon >= LastMonth", new PredefinedVariables()).ToList();
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/LXuoU0' %}  

As you can see, variables can be provided in multiple ways:

* Anonymous type
* `Dictionary<string, object>`
* `ExpandoObject`
* Class instance

Choose the approach that best fits your application and how you manage your predefined variables.

## Use LINQ Dynamic with the Execute Method

The LINQ Dynamic [Execute](/linq-dynamic#linq-execute-method) method is the most flexible method in our library. Not only can users dynamically filter a query, but they can also execute any LINQ operations they need within the expression.

In this example, we will query customers in the database and:

* Filter the customers to return
* Order the returned customers
* Select only the columns we want to return

```csharp
// @nuget: Z.Expressions.Eval
using System.Linq;
using Z.Expressions;

var environmentVariables = new Dictionary<string, object>();

// ADD Status variables
environmentVariables.Add("IsActive", CustomerStatus.IsActive);
environmentVariables.Add("IsDeleted", CustomerStatus.IsDeleted);
environmentVariables.Add("IsSuspended", CustomerStatus.IsSuspended);

// ADD DateTime variable
environmentVariables.Add("LastWeek", DateTime.Now.AddDays(-7));
environmentVariables.Add("LastMonth", DateTime.Now.AddMonths(-1));
environmentVariables.Add("LastYear", DateTime.Now.AddYears(-1));			

// In this example, we will query customers in the database and:
// - Filter the customers to return
// - Order the returned customers
// - Select columns that we return
var customers = context.Customers.Execute<IEnumerable>("Where(x => x.Status == IsActive && x.LastLogon >= LastMonth).Select(x => new { x.CustomerID, x.Name }).OrderBy(x => x.CustomerID).ToList()", environmentVariables);
```

{% include component-try-it.html href='https://dotnetfiddle.net/l1qn8c' %}  

One major difference with the `Execute` method is that you call the standard LINQ methods directly rather than their dynamic counterparts.

For example, inside the expression you use `Where`, `Select`, `OrderBy`, and any other LINQ methods you need. This makes sense because the purpose of the `Execute` method is to execute C# code dynamically at runtime.

## Use LINQ Dynamic with Other Methods

Throughout this getting started guide, we have explored only a few LINQ Dynamic methods. However, our library supports most LINQ methods through their dynamic counterparts.

- Deferred Methods
   - [DistinctDynamic](/linq-dynamic#linq-distinctdynamic-method)
   - [GroupByDynamic](/linq-dynamic#linq-groupbydynamic-method)
   - [OrderByDescendingDynamic](/linq-dynamic#linq-orderbydescendingdynamic-method)
   - [OrderByDynamic](/linq-dynamic#linq-orderbydynamic-method)
   - [ReverseDynamic](/linq-dynamic#linq-reservedynamic-method)
   - [SelectDynamic](/linq-dynamic#linq-selectdynamic-method)
   - [SelectManyDynamic](/linq-dynamic#linq-selectmanydynamic-method)
   - [SkipWhileDynamic](/linq-dynamic#linq-skipwhiledynamic-method)
   - [TakeWhileDynamic](/linq-dynamic#linq-takewhiledynamic-method)
   - [ThenByDescendingDynamic](/linq-dynamic#linq-thenbydescendingdynamic-method)
   - [ThenByDynamic](/linq-dynamic#linq-thenbydynamic-method)
   - [WhereDynamic](/linq-dynamic#linq-wheredynamic-method)

- Immediate Methods
   - [AllDynamic](/linq-dynamic#linq-alldynamic-method)
   - [AnyDynamic](/linq-dynamic#linq-anydynamic-method)
   - [CountDynamic](/linq-dynamic#linq-countdynamic-method)
   - [ElementAtDynamic](/linq-dynamic#linq-elementatdynamic-method)
   - [FirstDynamic](/linq-dynamic#linq-firstdynamic-method)
   - [FirstOrDefaultDynamic](/linq-dynamic#linq-firstordefaultdynamic-method)
   - [LastDynamic](/linq-dynamic#linq-lastdynamic-method)
   - [LastOrDefaultDynamic](/linq-dynamic#linq-lastordefaultdynamic-method)
   - [LongCountDynamic](/linq-dynamic#linq-longcountdynamic-method)
   - [MaxDynamic](/linq-dynamic#linq-maxdynamic-method)
   - [MinDynamic](/linq-dynamic#linq-mindynamic-method)
   - [SelectToListDynamic](/linq-dynamic#linq-selecttolistdynamic-method)
   - [SelectManyToListDynamic](/linq-dynamic#linq-selectmanytolistdynamic-method)
   - [SingleDynamic](/linq-dynamic#linq-singledynamic-method)
   - [SingleOrDefaultDynamic](/linq-dynamic#linq-singleordefaultdynamic-method)
   - [SumDynamic](/linq-dynamic#linq-sumdynamic-method)
   - [ToArrayDynamic](/linq-dynamic#linq-toarraydynamic-method)
   - [ToListDynamic](/linq-dynamic#linq-tolistdynamic-method)

As you can see, we added the `Dynamic` suffix to all our methods to avoid confusion with standard LINQ methods.

You can use these extension methods with the following types:

* `IEnumerable`
* `IEnumerable<T>`
* `IQueryable`
* `IQueryable<T>`

## Summary

In this getting started tutorial, you learned how to use LINQ Dynamic with the [WhereDynamic](/linq-dynamic#linq-wheredynamic-method) method, the [Execute](/linq-dynamic#linq-execute-method) method, and discovered the other supported [LINQ Dynamic](/linq-dynamic) methods available in our library.

If you already know LINQ, learning LINQ Dynamic should be very straightforward since the syntax remains almost identical.

As we have also seen, LINQ Dynamic is compatible with EF Core and other ORMs. Under the hood, our library does not interpret queries itself. Instead, it creates the required lambda expressions and calls the underlying LINQ methods at runtime.

If you are looking for a library that interprets expressions and translates them into LINQ expressions, you may prefer our other popular [System.Linq.Dynamic library](https://dynamic-linq.net/).