---
Name: Execute Methods
---

# How to use the C# Eval Execute Methods

The C# Eval Expression library is as powerful as it is easy to use. The library allows you to execute C# code dynamically and even very complex code but everything in a straightforward manner.

First, let's see all overload methods that allow you to pass C# code to execute dynamically, and then we will explain them:

| Category          | Name                                                                | Description                                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Static Methods    | Eval.Execute(string code)                                           | Execute the code under the global context and return an object.                                                                                             |
| Static Methods    | Eval.Execute(string code, object parameters)                        | Execute the code under the global context using the parameter specified (single value, anonymous type, dictionary) and return an object.                    |
| Static Methods    | Eval.Execute(string code, params object\[\] parameters)             | Execute the code under the global context using parameters specified and return an object.                                                                  |
| Static Methods    | Eval.Execute<TResult>(string code)                                  | Execute the code under the global context and return a TResult                                                                                              |
| Static Methods    | Eval.Execute<TResult>(string code, object parameters)               | Execute the code under the global context using the parameter specified (single value, anonymous type, dictionary) and return a TResult                     |
| Static Methods    | Eval.Execute<TResult>(string code, params object\[\] parameters)    | Execute the code under the global context using parameters specified and return a TResult                                                                   |
| Instance Methods  | context.Execute(string code)                                        | Execute the code under this instance context and return an object.                                                                                          |
| Instance Methods  | context.Execute(string code, object parameters)                     | Execute the code under this instance context using the parameter specified (single value, anonymous type, dictionary) and return an object.                 |
| Instance Methods  | context.Execute(string code, params object\[\] parameters)          | Execute the code under this instance context using parameters specified and return an object.                                                               |
| Instance Methods  | context.Execute<TResult>(string code)                               | Execute the code under this instance context and return a TResult                                                                                           |
| Instance Methods  | context.Execute<TResult>(string code, object parameters)            | Execute the code under this instance context using the parameter specified (single value, anonymous type, dictionary) and return a TResult                  |
| Instance Methods  | context.Execute<TResult>(string code, params object\[\] parameters) | Execute the code under this instance context using parameters specified and return a TResult                                                                |
| Extension Methods | "code".Execute()                                                    | Extend a string to execute the code under the global context and return an object.                                                                          |
| Extension Methods | "code".Execute(object parameters)                                   | Extend a string to execute the code under the global context using the parameter specified (single value, anonymous type, dictionary) and return an object. |
| Extension Methods | "code".Execute(params object\[\] parameters)                        | Extend a string to execute the code under the global context using parameters specified and return an object.                                               |
| Extension Methods | "code".Execute<TResult>()                                           | Extend a string to execute the code under the global context and return a TResult                                                                           |
| Extension Methods | "code".Execute<TResult>(object parameters)                          | Extend a string to execute the code under the global context using the parameter specified (single value, anonymous type, dictionary) and return a TResult  |
| Extension Methods | "code".Execute<TResult>params object\[\] parameters)                | Extend a string to execute the code under the global context using parameters specified and return a TResult                                                |

If we quickly break down those methods, there are three parts to understand:

- [The "code"](#the-code)
- [The return type](#the-return-type)
- [The parameters](#the-parameters)

## The "code"

You can execute a c# code dynamically in 3 different ways:

- Using a static method (`Eval.Execute`)
- Using an instance method (`context.Execute`)
- Using an extension method (`"code".Execute`)

In the case of the `Eval.Execute` and `"code".Execute`, a global context is used. But what is the global context? That is simply an instance of a [EvalContext](/eval-context) but stored in a static variable in the [EvalManager](/eval-manager).

In the case of the `context.Execute`, you are the one that creates a new instance of the [EvalContext](/eval-context) and then uses it.

So now the second question should probably be when should you use the global context or an instance context? We explain the answer more detail in the [EvalContext](/eval-context) documentation, but in short:

- If you don't need to customize the context for this evaluation, you should use the global context
- If you need to customize the context for this evaluation, you should use an instance context

```csharp
```

## The return type

You have the choice to specify or not a return type. If you have a `TResult`, the returned value will be of `TResult` type. Otherwise, the returned value will be of `object` type.

Even for an expression that doesn't return anything, such as `var x = 2 + 3`,  our library still returns something, as this is how the Expression Tree work. In this case, it returns the value of `x`, which is `5`, and it is up to you to use or not use it.

```csharp
```

## The parameters

There are different ways to pass parameters within our library:

- Using an Anonymous Type
- Using a Class Instance
- Using a Dictionary<string, TValue>
- Using an Expando Object
- Using parameter values

For the case of `Anonymous Type`, `Class Instance`, or `ExpandoObject`, you can use member names directly in your expression as our library know them.

For the case of `Dictionary<string, TValue>`, you can use the key names directly in your expression as our library know them.

For the case of `parameter values`, you will need to use their position, as our library is unaware of the variable name you are passing.

There is no best way for how you should pass parameters. It all depends on your scenario and how you would like to use our library. Still, the most common way is probably using anonymous type when you know in advance the values and a dictionary when you need even your parameter to be dynamic.

```csharp
// Parameter: Anonymous Type
object result = Eval.Execute("X + Y", new { X = 1, Y = 2} );

// Parameter: Argument Position
object result = Eval.Execute("{0} + {1}", 1, 2);

// Parameter: Class Member
dynamic expandoObject = new ExpandoObject();
expandoObject.X = 1;
expandoObject.Y = 2;
object result = Eval.Execute("X + Y", expandoObject);

// Parameter: Dictionary Key
var values = new Dictionary<string, object>() { {"X", 1}, {"Y", 2} };
object result = Eval.Execute("X + Y", values);
```
{% include component-try-it.html href='https://dotnetfiddle.net/8mtLH8' %}