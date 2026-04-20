# My First Evaluation with C# Eval Expression

C# is a powerful programming language, but it does not include an **eval** method like some other languages, such as [JavaScript](https://riptutorial.com/javascript/topic/7080/evaluating-javascript).

The C# Eval Expression library is the best solution to overcome this limitation. It lets you quickly evaluate code at runtime with very little code on your side.

This is especially useful when working with dynamic logic such as user input, formulas, or rules that you don’t want to hardcode or recompile.

In this tutorial, you will learn how to use our library to:

* [Evaluate a C# expression](#evaluate-a-c-expression)
* [Evaluate a C# expression with variables](#evaluate-a-c-expression-with-variables)
* [Evaluate a C# expression with a return type](#evaluate-a-c-expression-with-a-return-type)
* [Evaluate a C# expression from a string](#evaluate-a-c-expression-from-a-string)
* [Evaluate a C# expression from a context](#evaluate-a-c-expression-from-a-context)

## Evaluate a C# expression

The simplest way to execute a C# expression at runtime is by using the `Eval.Execute` method. Under the hood, it calls the `Execute` method from the `DefaultContext`, which you will learn more about later in this tutorial.

The `Execute` method takes the code to execute as its first parameter.

In this example, we dynamically create a list of `int` and filter it using LINQ to return only items greater than `2`.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

// NOTE: The list returned contains "3" and "4"
var list = Eval.Execute(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");
```

{% include component-try-it.html href='https://dotnetfiddle.net/KdkAbb' %}

The result is a list containing the values 3 and 4.

The C# Eval library supports much more than this simple example. However, we keep it very basic here to help you get started quickly.

## Evaluate a C# expression with variables

In the previous example, we created the list dynamically inside the `Execute` method and hardcoded the value `2` used for filtering. In real scenarios, those values usually come from existing variables or user input.

The `Execute` method allows you to pass variables to your expression as the second parameter (or all remaining parameters).

There are multiple ways to pass variables, such as:

* Anonymous type
* Class instance
* Dictionary
* Expando object
* Values

In most cases, using an anonymous type is the simplest and recommended approach.

In this example, we create the `list` and `greaterThan` variables and demonstrate different ways to pass them to the expression.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

// Passing parameters with an Anonymous Type
{
	var rList = Eval.Execute("list.Where(x => x > greaterThan)", new { list, greaterThan });
}		

// Passing parameters with an Anonymous Type using named members
{
	var rList = Eval.Execute("listName.Where(x => x > greaterThanName)", new { listName = list, greaterThanName = greaterThan });
}		

// Passing parameters with a class instance and using class members
// NOTE: When only using 1 parameter, you can directly use member names, "Item1" and "Item2" in the case of a Tuple<,>
{
	var rList = Eval.Execute("Item1.Where(x => x > Item2)", new Tuple<List<int>, int>(list, greaterThan));
}		

// Passing parameters with a Dictionary and using key names
// NOTE: When only using 1 parameter, you can directly use dictionary key names in the expression
{
	var dictionary = new Dictionary<string, object>();
	dictionary.Add("list", list);
	dictionary.Add("greaterThan", greaterThan);
	var rList = Eval.Execute("list.Where(x => x > greaterThan)", dictionary);
}

// Passing parameters using an Expando Object and using member names
// NOTE: When only using 1 parameter, you can directly use member names of the Expando Object
{
	dynamic expandoObject = new ExpandoObject();
	expandoObject.list = list;
	expandoObject.greaterThan = greaterThan;
	var rList = Eval.Execute("list.Where(x => x > greaterThan)", expandoObject);
}

// Passing parameters directly with the Values
// NOTE: Because parameters are not named, you need to use the position as our library is not aware of the name "list" and "greaterThan"
{
	var rList = Eval.Execute("{0}.Where(x => x > {1})", list, greaterThan);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/t8KHIX' %}  

## Evaluate a C# expression with a return type

In the previous examples, we learned how to execute code dynamically and pass variables to our expressions.

Another important concept is specifying a return type. If no return type is specified, the result is returned as an `object`.

The `Execute<TReturn>` method lets you specify the expected return type using a generic parameter.

In this example, we reuse the `list` and `greaterThan` variables, but this time we specify the return type as `List<int>`.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

list = Eval.Execute<List<int>>("list.Where(x => x > greaterThan)", new { list, greaterThan });
```

{% include component-try-it.html href='https://dotnetfiddle.net/D5R1Wm' %} 

Did you notice something interesting in this example?

The expression actually returns an `IEnumerable<int>`, not a `List<int>`. However, the library automatically handles this for you by calling `ToList()` to match the expected return type. That’s one of the many ways the C# Eval Expression library simplifies your code.

## Evaluate a C# expression from a string

If you have read the previous sections, this part should be straightforward.

Our library adds a convenient `Execute` extension method to the `string` type. Passing parameters and specifying a return type works exactly the same as before. The only difference is that the expression comes from the string instance.

In this example, we store the expression in a string variable and call the `Execute` method directly on it.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

var expressionToExecute = "list.Where(x => x > greaterThan)";
list = expressionToExecute.Execute<List<int>>(new { list, greaterThan });

// SIMILAR TO: list = Eval.Execute<List<int>>("list.Where(x => x > greaterThan)", new { list, greaterThan }); 
```

{% include component-try-it.html href='https://dotnetfiddle.net/lZXP5O' %}

## Evaluate a C# expression from a context

All the `Execute` methods we used in previous examples rely on a global [EvalContext](/eval-context) stored in [EvalManager.DefaultContext](/eval-manager#defaultcontext). In other words:

* Using `Eval.Execute` is similar to calling `EvalManager.DefaultContext.Execute`
* Using `"the_string_expression".Execute` is also similar to calling `EvalManager.DefaultContext.Execute`

What exactly is an [EvalContext](/eval-context)?

It’s a class you can instantiate to configure options specific to a particular execution. This is useful when you don’t want those options to be applied globally.

For example, you might want to:

* Enable [safe mode](/options#safe-mode)
* Define global variables
* Add custom methods

In this example, we create a context, add a custom extension method named `GreaterThan`, and then execute our expression.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

var context = new EvalContext();

context.AddMethod(@"
bool GreaterThan(this int x, int y)
{
	return x > y;
}
");

list = context.Execute<List<int>>("list.Where(x => x.GreaterThan(greaterThan))", new { list, greaterThan }); 
```

## Summary

In this getting started tutorial, you learned how to use the `Execute` method to dynamically evaluate code, pass parameters, and specify a return type.

Using the `Execute` method is straightforward, and most developers should already feel comfortable using it.

However, mastering it takes time. To fully take advantage of the library, make sure to explore all available [options](/options) in the `EvalContext` and continue reading the other tutorials and articles.