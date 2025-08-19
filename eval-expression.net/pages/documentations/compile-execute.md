---
Name: Compile & Execute Methods
LastMod: 2025-08-19
---

# Compile & Execute Methods

## Description
Compile and execute a C# expression at runtime.

## Execute
Execute a C# expression and return the result: 

- Execute&lt;TResult&gt;(string code)
- Execute&lt;TResult&gt;(string code, object parameters)
- Execute&lt;TResult&gt;(string code, params object[] parameters)
- Execute(string code)
- Execute(string code, object parameters)
- Execute(string code, params object[] parameters)

### Example
```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var context = new EvalContext();
// ... context options ...

string code = "Price * Quantity";
var price = context.Execute<decimal>(code, orderItem);
```
{% include component-try-it.html href='https://dotnetfiddle.net/tzBdMI' %}

## Compile
Compile a C# expression and return a delegate: 

- Compile&lt;TDelegate&gt;(string code)
- Compile&lt;TDelegate&gt;(string code, IEnumerable&lt;string&gt; parameterNames)
- Compile&lt;TDelegate&gt;(string code, params string[] parameterNames)
- Compile(string): **Func&lt;object&gt;**
- Compile(string, Type type1): **Func&lt;object, object&gt;**
- Compile(string, Type type1, ... , Type type9): **Func&lt;object, ... , object, object&gt;**
- Compile(string, IEnumerable&lt;Type&gt;): **Func&lt;IEnumerable, object&gt;**
- Compile(string, params Type[]): **Func&lt;IEnumerable, object&gt;**
- Compile(string, IDictionary&lt;string, Type&gt;): **Func&lt;IDictionary, object&gt;**

### Example
```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var context = new EvalContext();
// ... context options ...

string code = "Price * Quantity";
var compiled = context.Compile<Func<OrderItem, decimal>>(code);

decimal total = 0;
foreach(var item in list)
{
    total += compiled(item);
}
```
{% include component-try-it.html href='https://dotnetfiddle.net/00YSGK' %}
