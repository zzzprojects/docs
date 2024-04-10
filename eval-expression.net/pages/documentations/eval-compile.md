---
Name: Compile Method
LastMod: 2024-04-09
---

# How to use the Compile Method?

The `Compile` method allows you to compile any C# code or expression at runtime and return a delegate.

The `Compile` method offers the best performance when you need to execute the same expression multiple times but with different parameters:

```csharp
var fizzBuzzCompiled = Eval.Compile<Func<int, string>>(@"
if (i % 15 == 0)
	return $'{i} => FizzBuzz';
else if (i % 3 == 0)
	return $'{i} => Fizz';
else if (i % 5 == 0)
	return $'{i} => Buzz';
else
	return $'{i} => {i}';
", "i");
		
var answers = new List<string>();

for(int i = 0; i <= 100; i++)
{
	answers.Add(fizzBuzzCompiled(i));
}

Console.WriteLine(string.Join(Environment.NewLine, answers));
```

{% include component-try-it.html href='https://dotnetfiddle.net/T1tOSc' %}

The `Compile` method is slightly harder at first to understand than the [Execute](eval-execute) method. However, the `Compile` method offers the best performance as you directly use the delegate (compiled method).


## C# Compile Method

You can call the `Compile` method in 24 different ways:

- Instance Methods:
   - `context.Compile(string code)`
   - `context.Compile(string code, IEnumerable<Type> parameterTypes)`
   - `context.Compile(string code, params Type[] parameterTypes)`
   - `context.Compile(string code, Type type1)`
   - `context.Compile(string code, Type type1, ..., Type type9)`
   - `context.Compile<TDelegate>(string code)`
   - `context.Compile<TDelegate>(string code, IEnumerable<string> parameterNames)`
   - `context.Compile<TDelegate>(string code, params string[] parameterNames)`
- Static Methods:
   - `Eval.Compile(string code)`
   - `Eval.Compile(string code, IEnumerable<Type> parameterTypes)`
   - `Eval.Compile(string code, params Type[] parameterTypes)`
   - `Eval.Compile(string code, Type type1)`
   - `Eval.Compile(string code, Type type1, ..., Type type9)`
   - `Eval.Compile<TDelegate>(string code)`
   - `Eval.Compile<TDelegate>(string code, IEnumerable<string> parameterNames)`
   - `Eval.Compile<TDelegate>(string code, params string[] parameterNames)`
- Extension Methods:
   - `"code".Compile()`
   - `"code".Compile(IEnumerable<Type> parameterTypes)`
   - `"code".Compile(params Type[] parameterTypes)`
   - `"code".Compile(Type type1)`
   - `"code".Compile(Type type1, ..., Type type9)`
   - `"code".Compile<TDelegate>()`
   - `"code".Compile<TDelegate>(IEnumerable<string> parameterNames)`
   - `"code".Compile<TDelegate>(params string[] parameterNames)`

We can easily break our 24 methods into 3 topics to quickly understand them:

- [The code](#the-code)
- [The delegate / parameter type](#the-delegate-parameter-type)
- [The parameter names](#the-parameter-names)

### The code

This part is exactly like the [Execute Method](/eval-execute#the-code).

You can provide the code to compile in 3 different ways:

1. **Instance method:** `context.Compile(code)`
2. **Static method:** `Eval.Compile(code)`
3. **Extension method:** `"code".Compile`

1 - In the case of the **instance method** (`context.Compile(code)`). First, you create a new instance of the [EvalContext](/eval-context), and then you can use the `Compile` method from the context created:

```csharp
// CREATE a new instance of EvalContext
var context = new EvalContext();

// USE the `Compile` method from the context created
var compiledList1 = context.Compile<Func<List<int>>>("new List<int>() { 1, 2, 3 };");

var list1 = compiledList1();
```

{% include component-try-it.html href='https://dotnetfiddle.net/mzaxf5' %}

2 - In the case of the **static method** (`Eval.Compile(code)`), you can directly call the static `Eval.Compile` method, which uses a global context under the hood: 

```csharp
// CALL the static `Eval.Compile` method
var compiledList2 = Eval.Compile<Func<List<int>>>("new List<int>() { 1, 2, 3 };");

var list2 = compiledList2();
```

{% include component-try-it.html href='https://dotnetfiddle.net/mzaxf5' %}

3 - In the case of the **extension method** (`"code".Compile`), you can directly call the `Compile` method that extends the string type, which uses a global context under the hood:
```csharp
// CALL the `Compile` method that extends the string type
var compiledList3 = "new List<int>() { 1, 2, 3 };".Compile<Func<List<int>>>();

var list3 = compiledList3();
```

{% include component-try-it.html href='https://dotnetfiddle.net/mzaxf5' %}

One of the major differences is whether you want to create a new instance of the [EvalContext](/eval-context) or use the global context (an instance stored in a static variable).

We explain the answer more detail in the [EvalContext](/eval-context) documentation, but in short:

- If you need to customize the context for this specific compilation, **you should** use an instance context
- If you don't need to customize the context for this specific compilation, **you can** use the global context

### The delegate / parameter type

The delegate is the method signature representing what parameter is allowed and the returned type. A delegate is either a `Func` when returning a value or an `Action` when there is no return.

For example, `Func<string, int, double>` is similar to `public double MyMethodName(int, double)`. If you don't wish to return a type, you must use an Action such as `Action<string, int>` is similar to `public void MyMethodName(int, double)`.

Most of the time, you will want to specify the delegate yourselves, but this is optional as we can create one for you depending on the list of types you specified or how you pass the parameter names.

For example, if you don't specify a delegate but specify 2 types, such as `Compile(code, int, int)`, we will create a `Func<int, int, object>` delegate. Also, if you don't specify a delegate, you will need to use position in the expression, such as `{0}` corresponds to the first type, as there is currently no way to pass parameter names.

In the following example, you will find multiple uses of the delegate:

```csharp
// 1. Eval.Compile<Func<int, int, int, List<int>>>(code)
{
	var compiledList1 = Eval.Compile<Func<int, int, int, List<int>>>("var list = new List<int>(); list.Add({0}); list.Add({1}); list.Add({2}); return list;");
	
	var list1 = compiledList1(2, 4, 6);
}


// 2. Eval.Compile(code, typeof(int), typeof(int), typeof(int));
{
	var compiledList2 = Eval.Compile("var list = new List<int>(); list.Add({0}); list.Add({1}); list.Add({2}); return list;", typeof(int), typeof(int), typeof(int));
	
	var list2 = (List<int>)compiledList2(3, 5, 7);
}

// 3. Eval.Compile<Action<List<int>>>(code);
{
	var list3 = new List<int>();
	var compiledAction = Eval.Compile<Action<List<int>>>("{0}.Add(4);{0}.Add(6);{0}.Add(8);");
	compiledAction(list3);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/EztG9N' %}

### The parameter names

Using parameter names allows you to directly use those names in the expression instead of using their position.

If you don't specify a delegate, there is currently no way to specify parameter names, so you will need to keep using their parameter position.

If you specify a delegate, you can specify parameter names allowed in the expression by passing them via an `IEnumerable` or `params`.

In the following example, you will find multiple uses of how using parameter names in your dynamic C# expression:

```csharp
var compiledList1 = Eval.Compile<Func<int, int, int, List<int>>>("new List<int>() { A, B, C };", "A", "B", "C");

var list1 = compiledList1(2, 4, 6);
```
{% include component-try-it.html href='https://dotnetfiddle.net/ZnnBVz' %}

## Compile Async

The C# Eval library doesn't have `CompileAsync` methods. Under the hood, we compile the expression which the [Expression<TDelegate>.Compile method](https://learn.microsoft.com/en-us/dotnet/api/system.linq.expressions.expression-1.compile), which doesn't have a `CompileAsync` method either.

However, the `Compile` method support `Async` expression, but the task needs to be handled on the [Execute](/eval-execute#execute-async) part and not the compile part:

1. **Return a task from the delegate:** `Eval.Compile<Func<Task>>(code)`
2. **Handle the task** using `await` in the code

1 - If you **return a task from the delegate** (`Eval.Compile<Func<Task>>(code)`), you need to handle the task with `await` outside the code you want to compile and execute dynamically:

```csharp
var compiled1 = Eval.Compile<Func<string, Task<string>>>("File.ReadAllTextAsync(fileName)", "fileName");		
var text1 = await compiled1(fileName);
```

{% include component-try-it.html href='https://dotnetfiddle.net/nuxjmp' %}

2 - If you **handle the task**, you need to use `await` inside the code you want to compile and execute dynamically:

```csharp
var compiled2 = Eval.Compile<Func<string, string>>("await File.ReadAllTextAsync(fileName)", "fileName");
var text2 = compiled2(fileName);
```

{% include component-try-it.html href='https://dotnetfiddle.net/nuxjmp' %}

## Conclusion

In conclusion, you can call the `Compile` method in 24 different ways, but if you understand the concept, they all look very similar, and we can resume them to this: Do you want to use the `Compile` method:

- With an instance or a global context
- With or without a delegate/parameter type
- With or without parameter names

For people beginning with our library, we recommend using the [Execute](/eval-execute) until they get comfortable with the method before moving to the `Compile` method, which is slightly more complex.

If you want to learn more about the `Compile` method, we recommend reading the [My First Compilation](/my-first-compilation) article.