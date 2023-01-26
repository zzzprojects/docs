---
Name: Execute Method
---

# How to use the Execute Method?

The `Execute` method allows you to execute any C# code or expression at runtime.

It could be from a very simple math expression from an user input:

```csharp
// Let's assume the user input is from someone that wants to solve a Facebook math problem
var userInput = "6-1*0";

// Execute the user input to solve the math expression
var r = Eval.Execute<int>(userInput); // return 6

// The answer is 6 due to multiply operator priority "6-(1*0)" => "6-0" => "6"
Console.WriteLine("The result is: " + r);
```

{% include component-try-it.html href='https://dotnetfiddle.net/Jf9LMp' %}

To a more complex C# code, such as solving the famous [FizzBuzz](https://en.wikipedia.org/wiki/Fizz_buzz) interview question:

```csharp
var list = Eval.Execute<List<string>>(@"

var answers = new List<string>();

for (int i = 1; i <= 100; i++)
{
    if (i % 15 == 0)
        answers.Add($'{i} => FizzBuzz');
    else if (i % 3 == 0)
        answers.Add($'{i} => Fizz');
    else if (i % 5 == 0)
        answers.Add($'{i} => Buzz');
    else
        answers.Add($'{i} => {i}');
}

return answers;

");

Console.WriteLine(string.Join(Environment.NewLine, list));
```

{% include component-try-it.html href='https://dotnetfiddle.net/BBJ4Ut' %}

In short, the `Execute` method from the C# Eval library let you execute any C# code at runtime that you usually write in Visual Studio.

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
 
Indeed 18 methods seem a lot and overkill. However, we can easily break them into 3 topics to quickly understand them:

- [The code](#the-code)
- [The return type](#the-return-type)
- [The parameters](#the-parameters)
 
### The code

You can provide the code to execute in 3 different ways:

1. **Instance method:** `context.Execute(code)`
2. **Static method:** `Eval.Execute(code)`
3. **Extension method:** `"code".Execute`

1 - In the case of the **instance method** (`context.Execute(code)`). First, you create a new instance of the [EvalContext](/eval-context), and then you can use the `Execute` method from the context created:

```csharp
// CREATE a new instance of EvalContext
var context = new EvalContext();

// USE the `Execute` method from the context created
var list1 = context.Execute<List<int>>("new List<int>() { 1, 2, 3 };");
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

2 - In the case of the **static method** (`Eval.Execute(code)`), you can directly call the static `Eval.Execute` method, which uses a global context under the hood. 

```csharp
// CALL the static `Eval.Execute` method
var list2 = Eval.Execute<List<int>>("new List<int>() { 1, 2, 3 };");
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

3 - In the case of the **extension method** (`"code".Execute`), you can directly call the `Execute` method that extends the string type, which uses a global context under the hood. 
```csharp
// CALL the `Execute` method that extends the string type
var list3 = "new List<int>() { 1, 2, 3 };".Execute<List<int>>();
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

One of the major differences is whether you want to create a new instance of the [EvalContext](/eval-context) or use the global context (an instance stored in a static variable).

We explain the answer more detail in the [EvalContext](/eval-context) documentation, but in short:

- If you need to customize the context for this specific evaluation, **you should** use an instance context
- If you don't need to customize the context for this specific evaluation, **you can** use the global context

### The return type

When you use the `Execute` method, you have 2 choices that will affect the return type. You can specify:

1. **A TResult**: `Eval.Execute<TResult>(code)`
2. **Nothing**: `Eval.Execute(code)`

1 - If you use the generic `Execute` method and specify a `TResult` type (`Eval.Execute<TResult>(code)`), the method will return a `TResult`

```csharp
List<int> list1 = Eval.Execute<List<int>>("new List<int>() { 1, 2, 3 };");
```

{% include component-try-it.html href='https://dotnetfiddle.net/Jqu2As' %}

2 - If you don't specify a return type (`Eval.Execute(code)`), the method will return an `object`. Under the hood, the `Eval.Execute<object>` method is called.

```csharp
object object2 = Eval.Execute("new List<int>() { 1, 2, 3 };");
List<int> list2 = (List<int>)object2;
```

{% include component-try-it.html href='https://dotnetfiddle.net/Jqu2As' %}

### The parameters

In this section, you will learn how to use parameters in our expression. We are now down to only understanding the method parameters part that gets repeated in all our `Execute` methods:

1. `(string code)`
2. `(string code, object parameters)`
3. `(string code, params object[] parameters)`

The first execute method `(string code)` doesn't allow you to pass parameters to the expression. So this one is easy to understand, the code is simply getting executed:

```csharp
var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > 1 && x < 5)");
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
	}
```

```csharp
	// AnonymousType #2
	{
		var minValue = 1;
		var maxValue = 5;
		
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > minValue && x < maxValue)", new { minValue, maxValue });
	}
```

```csharp
	// Class instance
	{		
		var minMaxValue = new MinMaxValue() { MinValue = 1, MaxValue = 5 };
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", minMaxValue);
	}
```

```csharp
	// Dictionary<string, object>
	{
		var dict = new Dictionary<string, object>();
		dict["MinValue"] = 1;
		dict["MaxValue"] = 5;
		
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", dict);
	}
```

```csharp
	// ExpandoObject
	{
		dynamic expando = new ExpandoObject();
		expando.MinValue = 1;
		expando.MaxValue = 5;
		
		var r = Eval.Execute<List<int>>("return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)", expando);
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
var expression = "return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > {0} && x < {1})";
var r = Eval.Execute<List<int>>(expression, 1, 5);
```

{% include component-try-it.html href='https://dotnetfiddle.net/Q0gWZ3' %}

In addition, unlike the second execute method we have seen, you cannot use member names or key names directly by default unless you use the [IncludeMemberFromAllParameters option](/options#includememberfromallparameters)

```csharp
public static void Main()
{
	var context = new EvalContext();
	
	// SET IncludeMemberFromAllParameters option
	context.IncludeMemberFromAllParameters = true;
	
	var parameter1 = new Parameter1() { MinValue = 1 };
	var parameter2 = new Parameter2() { MaxValue = 5 };
	
	var expression = "return new List<int>() { 1, 2, 3, 4, 5 }.Where(x => x > MinValue && x < MaxValue)";
	var r = context.Execute<List<int>>(expression, parameter1, parameter2);
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
   


## Execute Async

The C# Eval library supports `Async` but has no `ExecuteAsync` method.

Adding `Async` methods doesn't make sense, as your non-async code will not magically start to be executed asynchronously. A code runs asynchronously when you call a method that supports async and returns a `Task` or `Task<TResult>`.

To execute `Async`, you have 2 choices:

1. **Return a task:** `Eval.Execute<Task>(code)` or `Eval.Execute<Task<Result>>(code)`
2. **Handle the task** using `await` in the code

1 - If you **return a task** (`Eval.Execute<Task>(code)` or `Eval.Execute<Task<Result>>(code)`), you need to handle the task with `await` outside the code you want to execute dynamically 

```csharp
var task = Eval.Execute<Task<string>>("File.ReadAllTextAsync(fileName)", new { fileName });
var text1 = await task;
```

{% include component-try-it.html href='https://dotnetfiddle.net/UPCTQT' %}

2 - If you **handle the task**, you need to use `await` inside the code you want to execute dynamically

```csharp
var text2 = Eval.Execute<string>("await File.ReadAllTextAsync(fileName)", new { fileName });
```

{% include component-try-it.html href='https://dotnetfiddle.net/UPCTQT' %}

## Conclusion

In conclusion, even if you can call the `Execute` method in 18 different ways, they look all the same and are pretty easy to understand as we can resume them to this: Do you want to use the `Execute` method:

- With an instance or a global context
- With or without a return type
- With or without parameters

You have seen in this documentation only some basic C# codes, but remember that our library is way more powerful than this, and pretty much all the C# languages are supported.

If you want to learn more about the `Execute` method, we recommend reading the [My First Evaluation](/my-first-evaluation) article. 

