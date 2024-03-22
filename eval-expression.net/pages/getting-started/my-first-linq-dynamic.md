---
Title: Execute LINQ Dynamic Expression with List and EF Core
MetaDescription: Learn how to use LINQ Dynamic query to execute methods such as Select, OrderBy, or Where clause dynamically.
LastMod: 2024-03-22
---

# My First LINQ Dynamic

LINQ is an incredibly powerful way of querying data. It allows developers to write code that is both expressive and easy to read. However, something that's missing is using LINQ with dynamic expression.

Indeed, you can use LINQ for most of your queries, but what to do if you wish to allow your user to dynamically filter a list for a report? That is when using LINQ Dynamic through the C# Eval Library starts to shine.

You can use LINQ Dynamic methods for `IEnumerable` and `IQueryable`. However, that also means you can use it for ORM, such as querying within Entity Framework Core and EF6.

In this tutorial, you will learn how to use our library to:

- [Use LINQ Dynamic with a WhereDynamic method](#use-linq-dynamic-with-a-wheredynamic-method)
- [Use LINQ Dynamic with a WhereDynamic method with variables](#use-linq-dynamic-with-a-wheredynamic-method-with-variables)
- [Use LINQ Dynamic with the Execute method](#use-linq-dynamic-with-the-execute-method)
- [Use LINQ Dynamic with other methods](#use-linq-dynamic-with-other-methods)

## Use LINQ Dynamic with a WhereDynamic method

The `Where` clause cannot have a dynamic expression. However, the C# Eval Expression library lets you easily achieve this behavior using the `WhereDynamic` method, which, under the hood, creates the lambda expression and call the `Where` method. So our library doesn't mimic and try to interpret the expression but instead really calls the `Where` LINQ method.

To use our method, you first must add the `using System.Linq;` directive to see LINQ Dynamic extension methods.

In this example, we assume a typical scenario where a developer uses [EF Core](https://www.learnentityframeworkcore.com/) and needs to filter a customer list depending on a custom filter entered by the end-user input. We will filter customers in the database to return only active customers who have logged in since the last month.

```csharp
var customers = context.Customers.WhereDynamic(x => "x.Status == Statut && x.LastLogon >= DateTime.Now.AddMonths(-1)").ToList();

// ...or...

var customers = context.Customers.WhereDynamic("x => x.Status == 0 && x.LastLogon >= DateTime.Now.AddMonths(-1)").ToList();
```

{% include component-try-it.html href='https://dotnetfiddle.net/b5EmTM' %}

As you can see in our example, you can pass the `input parameters` of the lambda expression in 2 different ways:

- Outside the expression `x => "expression"`
- Inside the expression `"x => expression"`

There is no "recommended way" to pass it, as both solutions are valid and supported. So uses the easiest and more readable way for you.

## Use LINQ Dynamic with a WhereDynamic method with variables

In the last example, we already saw how powerful the `WhereDynamic` method is. However, we sometimes also need to use it with variables.

For example, you can have some environment variables that will help your user to filter more easily. For instance, by having some predefined status, such as:

- `IsActive`
- `IsDeleted`
- `IsSuspended`

Or some variable to make the expression simpler such as:

- `LastWeek`
- `LastMonth`
- `LastYear`

In this example, we will use the same filter as the previous example (return only active customers who have logged in since the last month), but this time, with the help of variables:

```csharp
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

## Use LINQ Dynamic with the Execute method

The LINQ [Execute](/linq-dynamic#linq-execute-method) method is the most flexible of our LINQ Dynamic method. Not only can the user filter a query dynamically, but he can do whatever he wants in the expression.

In this example, we will query customers in the database and:

- Filter the customers to returns
- Order the returned customers
- Select columns that we return

```csharp
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
// - Filter the customers to returns
// - Order the returned customers
// - Select columns that we return
var customers = context.Customers.Execute<IEnumerable>("Where(x => x.Status == IsActive && x.LastLogon >= LastMonth).Select(x => new { x.CustomerID, x.Name }).OrderBy(x => x.CustomerID).ToList()", environmentVariables);
```

{% include component-try-it.html href='https://dotnetfiddle.net/l1qn8c' %}  

One major difference with the `Execute` method is that you call the LINQ method directly and not the `dynamic` counterpart. It makes sense as the `Execute` method behavior is really to execute C# code at runtime.


## Use LINQ Dynamic with other methods

Through this getting started about how to use LINQ Dynamic, we have seen a few methods, but almost all LINQ methods are supported:

- Deferred
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
- Immediate
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

As you can see, we added the `Dynamic` suffix to every one of our methods to avoid confusion.

You can use those extension methods to extend the following types:

- `IEnumerable`
- `IEnumerable<T>`
- `IQueryable`
- `IQueryable<T>`

## Conclusion

In this getting started tutorial, you learned how to use LINQ Dynamic with the [WhereDynamic](/linq-dynamic#linq-wheredynamic-method) method, the [Execute](/linq-dynamic#linq-execute-method) method, and the existence of all other supported [LINQ Dynamic](/linq-dynamic) methods.

If you already know how to use LINQ, using LINQ Dynamic should be very straightforward as the syntax is exactly the same.

As we also have seen, using LINQ Dynamic is also compatible with EF Core and other ORM as under the hood, our library doesn't interpret the expression but really calls the underlying LINQ method at runtime.