---
Name: Eval.Execute
---

# Eval.Execute

## Description
You can execute a C# expression at runtime using the <a href="https://eval-expression.net/" target="_blank">Eval-Expression.NET</a> library.

You can download it here: [Download](https://eval-expression.net/download)

> The Eval-Expression.NET library can be activated with the Bulk Operations license. 

You can specify parameter values to use in the expression in various ways:

- Anonymous Type
- Argument Position
- Class Member
- Dictionary

Under the hood, the first time an expression is executed, it's getting compiled and the delegate is stored in the memory before being returned and executed. All future calls from the same expression will retrieve the delegate from the memory to optimize the performance.

Even with this optimization, if you have to evaluate the same expression multiple times, for example, in a `for` loop, we highly recommend using the delegate returned from the Compile method directly instead.

## Execute and return a strongly typed result
You can return the result as a strongly typed type:

- Eval.Execute&lt;TResult&gt;(string code)
- Eval.Execute&lt;TResult&gt;(string code, object parameters)
- Eval.Execute&lt;TResult&gt;(string code, params object[] parameters)

### Example

```csharp
// Parameter: Anonymous Type
int result = Eval.Execute<int>("X + Y", new { X = 1, Y = 2} );

// Parameter: Argument Position
int result = Eval.Execute<int>("{0} + {1}", 1, 2);

// Parameter: Class Member
dynamic expandoObject = new ExpandoObject();
expandoObject.X = 1;
expandoObject.Y = 2;
int result = Eval.Execute<int>("X + Y", expandoObject);

// Parameter: Dictionary Key
var values = new Dictionary<string, object>() { {"X", 1}, {"Y", 2} };
int result = Eval.Execute<int>("X + Y", values);
```
{% include component-try-it.html href='https://dotnetfiddle.net/W9TwcP' %}  

## Execute and return an object result
You can return the result as an object:

- Eval.Execute(string code)
- Eval.Execute(string code, object parameters)
- Eval.Execute(string code, params object[] parameters)

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
