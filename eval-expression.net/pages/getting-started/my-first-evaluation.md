---
Title: Execute C# Code at Runtime - Get Started with your First Evaluation
MetaDescription: Learn how to execute a C# expression at runtime with parameter, return type, and from a dynamic string expression.
LastMod: 2024-03-22
---

# My First Evaluation

C# is a powerful programming language but lacks the **eval** method that some other languages have, such as [JavaScript](https://riptutorial.com/javascript/topic/7080/evaluating-javascript).

The C# Eval Expression library is the best solution to overcome this limitation and lets you quickly evaluate code at runtime with the minimum code required on your side.

In this tutorial, you will learn how to use our library to:

- [Evaluate a C# expression](#evaluate-a-c-expression)
- [Evaluate a C# expression with variables](#evaluate-a-c-expression-with-variables)
- [Evaluate a C# expression with a return type](#evaluate-a-c-expression-with-a-return-type)
- [Evaluate a C# expression from a string](#evaluate-a-c-expression-from-a-string)
- [Evaluate a C# expression from a context](#evaluate-a-c-expression-from-a-context)

## Evaluate a C# expression

The simplest way to execute a C# expression at runtime is by using the `Eval.Execute` method. Under the hood, it calls the `Execute` method from the `DefaultContext` that you will learn later in this tutorial.

The `Execute` method takes as the first parameter the code to execute. 

In this example, we will dynamically create a list of `int` and filter it using LINQ to return only items greater than 2. 

```csharp
// NOTE: The list returned contains "3" and "4"
var list = Eval.Execute(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");
```

{% include component-try-it.html href='https://dotnetfiddle.net/KdkAbb' %}  

Remember that the C# Eval library supports more than this basic statement. However, we will keep it very simple in our getting started section.

## Evaluate a C# expression with variables

In the last example, we created the list dynamically in the `Execute` method and hardcoded the value `2`, which we used to filter. However, those values will be from existing variables and user input in most real-life scenarios.

The `Execute` method takes variables to use in our expression as the second parameter (or all remaining parameters).

There are multiple different ways of passing variables, such as:

- Anonymous Type
- Class Member
- Dictionary
- Expando Object
- Values

In this example, we will create the `list` and `greaterThan` variables and show different ways to pass those variables to our expression.

```csharp
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
	expandoObject.greaterThan= greaterThan;
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

In the last two examples, we learned how to execute code dynamically and use variables in our expressions.

The last remaining part is how to specify a return type. If no return type is specified, the value is of type `object`.

The `Execute<TReturn>` method takes as the generic type the type to return.

In this example, we will continue with our getting started example by using our `list` and `greaterThan` variables but this time, specify the `List<int>` return type.

```csharp
var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

list = Eval.Execute<List<int>>("list.Where(x => x > greaterThan)", new { list, greaterThan });
```

{% include component-try-it.html href='https://dotnetfiddle.net/D5R1Wm' %}  

Did you spot an error in this example? The expression should return an `IEnumerable<int>` and not a `List<int>`! However, our library is smart enough and makes your life easy by automatically calling the `ToList` method to return the right type. That is one of the many advantages of using our library.

## Evaluate a C# expression from a string

If you have taken the time to read and understand the first three sections of this tutorial, the rest should be straightforward.

Our library adds a syntactic sugar execute extension method to extend the string type. Passing parameters and return type are similar to what we have previously seen, and the only difference is the code is taken from the string that the method extends.

In that example, we will set our expression in a string variable and call the `Execute` method that extends it.

```csharp
var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

var expressionToExecute = "list.Where(x => x > greaterThan)";
list = expressionToExecute.Execute<List<int>>(new { list, greaterThan });

// SIMILAR TO: list = Eval.Execute<List<int>>("list.Where(x => x > greaterThan)", new { list, greaterThan }); 
```

{% include component-try-it.html href='https://dotnetfiddle.net/lZXP5O' %}

## Evaluate a C# expression from a context

All the `Execute` methods we learned in previous examples use the global [EvalContext](/eval-context) stored in the [EvalManager.DefaultContext](/eval-manager#defaultcontext). In other words:

- using the `Eval.Execute` method is similar to doing `EvalManager.DefaultContext.Execute`.
- using the `"the_string_expression".Execute` method is similar to doing `EvalManager.DefaultContext.Execute`.

So anyway, what exactly is an [EvalContext](/eval-context)? That is a class you instancies to set [options](/options) specific to the current context that you do not want to apply globally to all your evaluation.

For example, you would like to run an expression in [safe mode](/options#safe-mode), specify some global variables, or even use the `AddMethod`.

In this example, we will create an instance context, add a new extension method named `GreaterThan` and use the `Execute` method.

```csharp
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

{% include component-try-it.html href='https://dotnetfiddle.net/KHRedn' %}

The method `GreaterThan` only exists for this context. That means the `Eval.Execute` is unaware and cannot use this method.

## Conclusion

In this getting started tutorial, you learned how to use the `Execute` method to dynamically evaluate some code, use parameters, and a return type.

Using the `Execute` method is straightforward, and most developers should already be able to use it without a problem. However, only time will allow you to master this method and exploit all the potential through all [options](/options) of the `EvalContext` and the possibility that the library offers, so make sure to continue to read other tutorials and articles.