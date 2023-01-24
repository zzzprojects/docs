---
Name: Execute Method
---

# How to use the Eval Execute Method?

The `Execute` method allows you to execute any C# code at runtime.

It could be from a very simple math expression using an user input:

```csharp
// Let's use a math formula found on Facebook as user input
var anotherFacebookMathEquation = "6-1*0+2/2";

// Let's check if C# Eval Expression library is smart enough to solve it
var r = Eval.Execute<int>(anotherFacebookMathEquation); // return 7

// The answer is 7 due to multiplication priority
Console.WriteLine("The result is: " + r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/Jf9LMp' %}

To a more complex C# code, such as solving the famous `FizzBuzz` interview questions:

```csharp
var list = Eval.Execute<List<string>>(@"
var rList = new List<string>();

for (int i = 1; i <= 100; i++)
{
    if (i % 15 == 0)
        rList.Add($'{i} = FizzBuzz');
    else if (i % 3 == 0)
		rList.Add($'{i} = Fizz');
    else if (i % 5 == 0)
        rList.Add($'{i} = Buzz');
    else
		rList.Add($'{i} = null');
}

return rList;
");

Console.WriteLine(string.Join(Environment.NewLine, list));
```

{% include component-try-it.html href='https://dotnetfiddle.net/BBJ4Ut' %}

In short, you can execute any C# code at runtime that you usually write in Visual Studio.

## C# Execute Method

You can call the `Execute` method in 18 different ways:

- Instance Methods:
   - context.Execute(string code)
   - context.Execute(string code, object parameters)
   - context.Execute(string code, params object[] parameters)
   - context.Execute<TResult>(string code)
   - context.Execute<TResult>(string code, object parameters)
   - context.Execute<TResult>(string code, params object[] parameters)
- Static Methods:
   - Eval.Execute(string code)
   - Eval.Execute(string code, object parameters)
   - Eval.Execute(string code, params object[] parameters)
   - Eval.Execute<TResult>(string code)
   - Eval.Execute<TResult>(string code, object parameters)
   - Eval.Execute<TResult>(string code, params object[] parameters)
- Extension Methods:
   - "code".Execute()
   - "code".Execute(object parameters)
   - "code".Execute(params object[] parameters)
   - "code".Execute<TResult>()
   - "code".Execute<TResult>(object parameters)
   - "code".Execute<TResult>params object[] parameters)
 
18 methods seem a lot and overkill, but we can easily break them into 4 topics to quickly understand them:

- [The code](#the-code)
- [The return type](#the-return-type)
- [The parameters](#the-parameters)
- [The async](#the-async)
 
### The code

You can pass the code (or often what we call the expression) to execute in 3 different ways:

- Using an instance method (`context.Execute`)
- Using a static method (`Eval.Execute`)
- Using an extension method (`"code".Execute`)

In the case of the instance method (`context.Execute`), you create a new of the [EvalContext](/eval-context) and call the `Execute` method from it:

```csharp
var context = new EvalContext();

var r = context.Execute<List<int>>("new List<int>() { 1, 2, 3 };");

FiddleHelper.WriteTable(r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

In the case of the static method (`Eval.Execute`) and extension method (`"code".Execute`), a global context is used.

But what is the global context? That is simply an instance of a [EvalContext](/eval-context), but stored in a static variable in the [EvalManager](/eval-manager). So both methods call under the hood the same method, but one takes the "code" as a parameter, and the other extends the string type, so the "code".

```csharp
// static method
var staticMethodResult = Eval.Execute<List<int>>("new List<int>() { 1, 2, 3 };");
FiddleHelper.WriteTable(staticMethodResult);

// extension method
var extensionMethodResult = "new List<int>() { 1, 2, 3 };".Execute<List<int>>();
FiddleHelper.WriteTable(staticMethodResult);
```

{% include component-try-it.html href='https://dotnetfiddle.net/buQimi' %}

As you see, the major difference is whether you want or not to create an instance of the [EvalContext](/eval-context), which brings us to the following question: When should you use an instance context or the global context?

We explain the answer more detail in the [EvalContext](/eval-context) documentation, but in short:

- If you need to customize the context for this specific evaluation, **you should** use an instance context
- If you don't need to customize the context for this specific evaluation, **you can** use the global context

We are now down to understand only **6 different methods** as we now know there is 3 different ways to call our 18 different methods (instance method, static method, extension method)

### The return type

When you use the `Execute` method, you have the choice to specify a `TResult` as return type `Execute<TResult>(code)` or not `Execute(code)`.

If you specify a return type (`<TResult>`), the returned value will be of `TResult` type:

```csharp
List<int> r = Eval.Execute<List<int>>("new List<int>() { 1, 2, 3 };");

FiddleHelper.WriteTable(r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/Jqu2As' %}


If you don't specify a return type, the returned value will be of `object` type:

```csharp
object r = Eval.Execute("new List<int>() { 1, 2, 3 };");

// you can still cast the return `object` type to the right type later
FiddleHelper.WriteTable((List<int>)r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/J0HLGG' %}


We are now down to understand only 3 different methods as we now know there is 2 different ways to call our 6 different methods (with and without a return type).

### The parameters

In this section, you will learn how to use parameters in our expression. We are now down to only understanding the method parameters part that gets repeated in all our `Execute` methods:

1. `(string code)`
2. `(string code, object parameters)`
3. `(string code, params object[] parameters)`

The first execute method `(string code)` doesn't allow you to pass parameters to the expression. So this one is easy to understand, the code is simply getting executed:

```csharp
var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > 1 && x < 5)");

FiddleHelper.WriteTable(r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/4vOu2H' %}

The second execute method `(string code, object parameters)` take an object as parameter.

The object parameter can be:

- An Anonymous Type
- A Class Instance
- A Dictionary<string, TValue>
- An Expando Object

To allow you to use more easily parameters, our library also automatically registers and lets you use in your expression:

- Member names in the case of `Anonymous Type`, `Class Instance`, or `ExpandoObject`
- Key names in the case of `Dictionary<string, TValue>`

```csharp
public static void Main()
{
	// AnonymousType #1
	{
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", new { MinValue = 1, MaxValue = 5 });
	
		FiddleHelper.WriteTable(r);
	}
	
	// AnonymousType #2
	{
		var minValue = 1;
		var maxValue = 5;
		
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > minValue && x < maxValue)", new { minValue, maxValue });
	
		FiddleHelper.WriteTable(r);
	}
	
	// Class instance
	{		
		var minMaxValue = new MinMaxValue() { MinValue = 1, MaxValue = 5 };
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", minMaxValue);
	
		FiddleHelper.WriteTable(r);
	}
	
	// Dictionary<string, object>
	{
		var dict = new Dictionary<string, object>();
		dict["MinValue"] = 1;
		dict["MaxValue"] = 5;
		
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", dict);
	
		FiddleHelper.WriteTable(r);
	}
	
	// ExpandoObject
	{
		dynamic expando = new ExpandoObject();
		expando.MinValue = 1;
		expando.MaxValue = 5;
		
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", expando);
	
		FiddleHelper.WriteTable(r);
	}
}

public class MinMaxValue
{
	public int MinValue { get; set; }
	public int MaxValue { get; set; }
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/wdLPTE' %}

In the third execute method `(string code, params object[] parameters)`, you can pass your parameter through the `params object[]`. You can learn more about [params here](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/params)

As parameters are not named (our library doesn't know your variable, property, or field name), you need to use positions such as `{0}` and `{1}` in the expression as you usually do with, for example, [string.Format](https://learn.microsoft.com/en-us/dotnet/api/system.string.format)

```csharp
var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > {0} && x < {1})", 1, 5);

FiddleHelper.WriteTable(r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/Q0gWZ3' %}

In addition, unlike the second execute method we have seen, you cannot use member names or key names directly by default unless you use the [IncludeMemberFromAllParameters option](/options#includememberfromallparameters)

```csharp
public static void Main()
{
	var context = new EvalContext();
	context.IncludeMemberFromAllParameters = true;
	var parameter1 = new Parameter1() { MinValue = 1 };
	var parameter2 = new Parameter2() { MaxValue = 5 };
	
	var r = context.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", parameter1, parameter2);
	
	FiddleHelper.WriteTable(r);
}

public class Parameter1
{
	public int MinValue { get; set; }
}

public class Parameter2
{
	public int MaxValue { get; set; }
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/MxDxx9' %}

Should you pass parameters for your expression through an anonymous type, a dictionary, or any other ways we have seen in this section? There is no best way to do it since everything depends on your scenario and preference.
   
By now, you should understand the 18 different ways to call the `Execute` method and if not, read this documentation a second time from the start before starting the `async` part. 

### The Async

Our library supports async code but has no method with the `Async` suffix.

We did it to avoid confusion, as many developers would think their code would get executed automatically asynchronously when it would not. What makes a code run asynchronously is when you call a method that supports async and returns a `Task` or `Task<TResult>`.

So our library support async, but you need to return the task as a return type through the `Execute` method, such as `Execute<Task<string>>`:

```csharp
var fileName = "myfile.txt";

await File.WriteAllTextAsync(fileName, "C# Eval Expression from ZZZ Projects");

var task = Eval.Execute<Task<string>>("File.ReadAllTextAsync(fileName)", new { fileName });

var text = await task;

Console.WriteLine(text);
```

{% include component-try-it.html href='https://dotnetfiddle.net/UPCTQT' %}

Or handle the task within the expression by using the `await` keyword:

```csharp
var fileName = "myfile.txt";

await File.WriteAllTextAsync(fileName, "C# Eval Expression from ZZZ Projects");

var text = Eval.Execute<string>("await File.ReadAllTextAsync(fileName)", new { fileName });

Console.WriteLine(text);
```

{% include component-try-it.html href='https://dotnetfiddle.net/oLFtz5' %}

## Conclusion

In conclusion, even if you can call the `Execute` method in 18 different ways, they look all the same and are pretty easy to understand as we can resume them to this: Do you want to use the `Execute` method

- With an instance or a global context
- With or without a return type
- With or without parameters for your expression

You have seen in this documentation only some basic C# codes, but remember that our library is way more powerful than this, and pretty much all the C# languages are supported.