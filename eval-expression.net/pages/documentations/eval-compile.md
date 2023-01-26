---
Name: Compile Method
---

# How to use the Compile Method?

The `Compile` method allows you to compile any C# code or expression at runtime and return a delegate.

Let's explain it with a very simple example:

1. First, we create a string that will contains our expression `X+Y`.
2. Then we will call the `Compile` method with a return type and parameter name (we will explain this later in this documentation)
   - We will specify the return type should be a `Func<int, int, int>`. That means that the first and second parameters are of type `int`, and the return value is also of type `int`.
   - We will specify that the first parameter name is `X` and the second parameter name is `Y`.
3. Finally, we will use the returned delegate in a `For` loop.

```csharp
// TODO
```

The `Compile` method is slightly harder to use than the [Execute](eval-execute) method. However, the `Compile` method offers the best performance as you directly use the delegate.

## C# Compile Method

You can call the `Compile` method in 24 different ways:

- Instance Methods:
   - context.Compile(string code)
   - context.Compile(string code, IEnumerable<Type> parameterTypes)
   - context.Compile(string code, params Type\[\] parameterTypes)
   - context.Compile(string code, Type type1)
   - context.Compile(string code, Type type1, ..., Type type9)
   - context.Compile<TDelegate>(string code)
   - context.Compile<TDelegate>(string code, IEnumerable<string> parameterNames)
   - context.Compile<TDelegate>(string code, params string\[\] parameterNames)
- Static Methods:
   - Eval.Compile(string code)
   - Eval.Compile(string code, IEnumerable<Type> parameterTypes)
   - Eval.Compile(string code, params Type\[\] parameterTypes)
   - Eval.Compile(string code, Type type1)
   - Eval.Compile(string code, Type type1, ..., Type type9)
   - Eval.Compile<TDelegate>(string code)
   - Eval.Compile<TDelegate>(string code, IEnumerable<string> parameterNames)
   - Eval.Compile<TDelegate>(string code, params string\[\] parameterNames)
- Extension Methods:
   - "code".Compile()
   - "code".Compile(IEnumerable<Type> parameterTypes)
   - "code".Compile(params Type\[\] parameterTypes)
   - "code".Compile(Type type1)
   - "code".Compile(Type type1, ..., Type type9)
   - "code".Compile<TDelegate>()
   - "code".Compile<TDelegate>(IEnumerable<string> parameterNames)
   - "code".Compile<TDelegate>(params string\[\] parameterNames)

We can easily break our 24 methods into 3 topics to quickly understand them:

- [The code](#the-code)
- [The delegate](#the-delegate)
- [The parameter names](#the-parameter-names)

## The code

This part is exactly like the [Execute Method](/eval-execute#the-code).

You can provide the code to compile in 3 different ways:

1. **Instance method:** `context.Compile(code)`
2. **Static method:** `Eval.Compile(code)`
3. **Extension method:** `"code".Compile`

1 - In the case of the **instance method** (`context.Compile(code)`). First, you create a new instance of the [EvalContext](/eval-context), and then you can use the `Compile` method from the context created:

```csharp
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

2 - In the case of the **static method** (`Eval.Compile(code)`), you can directly call the static `Eval.Compile` method, which uses a global context under the hood: 

```csharp
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

3 - In the case of the **extension method** (`"code".Compile`), you can directly call the `Compile` method that extends the string type, which uses a global context under the hood:
```csharp
```

{% include component-try-it.html href='https://dotnetfiddle.net/PCFOZJ' %}

One of the major differences is whether you want to create a new instance of the [EvalContext](/eval-context) or use the global context (an instance stored in a static variable).

We explain the answer more detail in the [EvalContext](/eval-context) documentation, but in short:

- If you need to customize the context for this specific compilation, **you should** use an instance context
- If you don't need to customize the context for this specific compilation, **you can** use the global context

## The delegate

The delegate is the method signature representing what parameter is allowed and the returned type. A delegate is either a `Func` when returning a value or an `Action` when there is no return.

For example, `Func<string, int, double>` is similar to `public double MyMethodName(int, double)`. If you don't wish to return a type, you must use an Action such as `Action<string, int>` is similar to `public void MyMethodName(int, double).

Most of the time, you will want to specify the delegate yourselves, but this is optional as we can create one for you depending on the list of types you specified or how you pass the parameter names.

For example, if you don't specify a delegate but specify 2 types, such as `Compile(code, int, int)`, we will create a `Func<int, int, object>` delegate. Also, if you don't specify a delegate, you will need to use position in the expression, such as `{0}` corresponds to the first type, as there is currently no way to pass parameter names.

In the following example, you will find multiple uses of the delegate:

```csharp
```

## The parameter names

Using parameter names allows you to directly use those names in the expression instead of using their position.

If you don't specify a delegate, there is currently no way to specify parameter names, so you will need to keep using their parameter position.

If you specify a delegate, you can specify parameter names allowed in the expression by passing them via an `IEnumerable` or `params`.

In the following example, you will find multiple uses of how using parameter names in your dynamic C# expression:

```csharp
// Delegate Func
var compiled = Eval.Compile<Func<int, int, int>>("{0} + {1}");
int result = compiled(1, 2);

// Delegate Action
var compiled = Eval.Compile<Action<int, int>>("{0} + {1}");
compiled(1, 2);

// Named Parameter
var compiled = Eval.Compile<Func<int, int, int>>("X + Y", "X", "Y");
int result = compiled(1, 2);
```
{% include component-try-it.html href='https://dotnetfiddle.net/MBHlX8' %}

## Compile Async

The C# Eval library doesn't have `CompileAsync` methods. Under the hood, we compile the expression which the [Expression<TDelegate>.Compile method](https://learn.microsoft.com/en-us/dotnet/api/system.linq.expressions.expression-1.compile), which doesn't have a `CompileAsync` method either.

However, the `Compile` method support `Async` expression, but the task needs to be handled on the [Execute](/eval-execute#execute-async) part and not the compile part:

1. **Return a task from the delegate:** `Eval.Compile<Func<Task>>(code)` or `Eval.Compile<Task<Result>>(code)`
2. **Handle the task** using `await` in the code

1 - If you **return a task from the delegate** (`Eval.Compile<Func<Task>>(code)` or `Eval.Compile<Task<Result>>(code)`), you need to handle the task with `await` outside the code you want to compile and execute dynamically:

```csharp
var task = Eval.Execute<Task<string>>("File.ReadAllTextAsync(fileName)", new { fileName });
var text1 = await task;
```

{% include component-try-it.html href='https://dotnetfiddle.net/UPCTQT' %}

2 - If you **handle the task**, you need to use `await` inside the code you want to compile and execute dynamically:

```csharp
var text2 = Eval.Execute<string>("await File.ReadAllTextAsync(fileName)", new { fileName });
```

{% include component-try-it.html href='https://dotnetfiddle.net/UPCTQT' %}

## Conclusion

In conclusion, you can call the `Compile` method in 24 different ways, but if you understand the concept, they all look very similar, and we can resume them to this: Do you want to use the `Compile` method:

- With an instance or a global context
- With or without a TDelegate
- With or without parameters

For people beginning with our library, we recommend using the [Execute](/eval-execute) until they get comfortable with the method before moving to the `Compile` method, which is slightly more complex.

If you want to learn more about the `Compile` method, we recommend reading the [My First Compilation](/my-first-compilation) article.

---


The C# Eval Expression library offers a method that lets you compile a C# expression dynamically that returns a method (a delegate). After, you can use the method created as many times as you need for the best performance.

Before continuing to read this doc, we highly recommend you read and understand the documentation about the [C# Execute Method](/eval-execute).

The primary reason why using the `Compile` method over the `Execute` method is performance. Under the hood, the `Execute` method uses the `Compile` method and then directly executes the created delegate. In comparison, the `Compile` method returns a delegate that you can use as many times as you wish. Even if the `Execute` method has some built-in caching mechanism that will let you re-use an existing delegate method, it will never be close to the performance of re-using the delegate method directly.

In other words, if you only need to execute an expression once, you should use the `Execute` method. However, if you need to execute the expression multiple times with different parameters, you should always use the `Compile` method.

To better understand the `Compile` method, let's see all overload methods, and then we will explain them:


 
| Category          | Name                                                                        | Description                                                                                                                                                        |
| ----------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Static Methods    | Eval.Compile(string code)                                                   | Compile the code under the global context and return a \`Func<object>\`.                                                                                           |
| Static Methods    | Eval.Compile(string code, IEnumerable<Type> parameterTypes)                 | Compile the code under the global context using types specified and return a \`Func<IEnumerable, object>\`.                                                        |
| Static Methods    | Eval.Compile(string code, params Type\[\] parameterTypes)                   | Compile the code under the global context using types specified and return a \`Func<IEnumerable, object>\`.                                                        |
| Static Methods    | Eval.Compile(string code, Type type1)                                       | Compile the code under the global context using the type specified and return a \`Func<object, object>\`.                                                          |
| Static Methods    | Eval.Compile(string code, Type type1, ..., Type type9)                      | Compile the code under the global context using types specified (up to 9 types are supported) and return a \`Func<object, …, object, object>\`.                    |
| Static Methods    | Eval.Compile<TDelegate>(string code)                                        | Compile the code under the global context and return a \`TDelegate\` (Action or Func).                                                                             |
| Static Methods    | Eval.Compile<TDelegate>(string code, IEnumerable<string> parameterNames)    | Compile the code under the global context using parameter names specified and return a \`TDelegate\` (Action or Func).                                             |
| Static Methods    | Eval.Compile<TDelegate>(string code, params string\[\] parameterNames)      | Compile the code under the global context using parameter names specified and return a \`TDelegate\` (Action or Func).                                             |
| Instance Methods  | context.Compile(string code)                                                | Compile the code under the instance context and return a \`Func<object>\`.                                                                                         |
| Instance Methods  | context.Compile(string code, IEnumerable<Type> parameterTypes)              | Compile the code under the instance context using types specified and return a \`Func<IEnumerable, object>\`.                                                      |
| Instance Methods  | context.Compile(string code, params Type\[\] parameterTypes)                | Compile the code under the instance context using types specified and return a \`Func<IEnumerable, object>\`.                                                      |
| Instance Methods  | context.Compile(string code, Type type1)                                    | Compile the code under the instance context using the type specified and return a \`Func<object, object>\`.                                                        |
| Instance Methods  | context.Compile(string code, Type type1, ..., Type type9)                   | Compile the code under the instance context using types specified (up to 9 types are supported) and return a \`Func<object, …, object, object>\`.                  |
| Instance Methods  | context.Compile<TDelegate>(string code)                                     | Compile the code under the instance context and return a \`TDelegate\` (Action or Func).                                                                           |
| Instance Methods  | context.Compile<TDelegate>(string code, IEnumerable<string> parameterNames) | Compile the code under the instance context using parameter names specified and return a \`TDelegate\` (Action or Func).                                           |
| Instance Methods  | context.Compile<TDelegate>(string code, params string\[\] parameterNames)   | Compile the code under the instance context using parameter names specified and return a \`TDelegate\` (Action or Func).                                           |
| Extension Methods | "code".Compile()                                                            | Extend a string to compile the code under the global context and return a \`Func<object>\`.                                                                        |
| Extension Methods | "code".Compile(IEnumerable<Type> parameterTypes)                            | Extend a string to compile the code under the global context using types specified and return a \`Func<IEnumerable, object>\`.                                     |
| Extension Methods | "code".Compile(params Type\[\] parameterTypes)                              | Extend a string to compile the code under the global context using types specified and return a \`Func<IEnumerable, object>\`.                                     |
| Extension Methods | "code".Compile(Type type1)                                                  | Extend a string to compile the code under the global context using the type specified and return a \`Func<object, object>\`.                                       |
| Extension Methods | "code".Compile(Type type1, ..., Type type9)                                 | Extend a string to compile the code under the global context using types specified (up to 9 types are supported) and return a \`Func<object, …, object, object>\`. |
| Extension Methods | "code".Compile<TDelegate>()                                                 | Extend a string to compile the code under the global context and return a \`TDelegate\` (Action or Func).                                                          |
| Extension Methods | "code".Compile<TDelegate>(IEnumerable<string> parameterNames)               | Extend a string to compile the code under the global context using parameter names specified and return a \`TDelegate\` (Action or Func).                          |
| Extension Methods | "code".Compile<TDelegate>(params string\[\] parameterNames)                 | Extend a string to compile the code under the global context using parameter names specified and return a \`TDelegate\` (Action or Func).                          |
