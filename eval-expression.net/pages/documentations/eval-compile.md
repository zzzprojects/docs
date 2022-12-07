---
Name: Eval.Compile Method
---

# How to use the C# Eval Compile Methods

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

If we quickly break down those methods, there are three parts to understand:

- [The "code"](#the-code)
- [The delegate](#the-delegate)
- [The parameter names](#the-parameter-names)

## The "code"

Exactly like the `Execute` method, the `Compile` method allows you to compile a C# expression dynamically in 3 different ways:

- Using a static method (`Eval.Compile`)
- Using an instance method (`context.Compile`)
- Using an extension method (`"code".Compile`)

Instead of repeating ourselves, if this part is not clear, see the [C# Execute Methods](/eval-execute#the-code) and the [Eval Context](/eval-context) documentation that will explain the difference in detail.

```csharp
```

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