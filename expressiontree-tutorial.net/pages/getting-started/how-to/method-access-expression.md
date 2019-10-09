# Method Access Expression

You can access any method by using `Expression.Call` which creates a MethodCallExpression that represents a call to a method. For example, you have the following code.

```csharp
string text = "This is simple text.";

Console.WriteLine(text.ToUpper());
```

Here is the code that is required to build the same functionality using expression tree. In this example, `string.ToUpper()` is called using an expression to convert a string to an upper case.

```csharp
string text = "This is simple text.";
Expression callExpr = Expression.Call(
Expression.Constant(text), typeof(String).GetMethod("ToUpper", new Type[] { }));

// The following statement first creates an expression tree,
// then compiles it and then executes it.  
Console.WriteLine(Expression.Lambda<Func<String>>(callExpr).Compile()());
```
[Try it online](https://dotnetfiddle.net/YRwj9E)

You can also call your custom methods using `Expression.Call`. For example, we have defined the following function which takes two integer arguments and returns the sum of them.

```csharp 
public class MyClass
{
    public static int MyFunc(int a, int b)
    {
        return a + b;
    }
}
```
`
The direct way to call this function is straightforward.

```csharp 
Console.WriteLine(MyClass.MyFunc(1,2));
```

It will return **3** as an output. Now to access this function using expression API, we can use the Expression.Call`.

```csharp
int param1 = 1;
int param2 = 2;
var c1 = Expression.Constant(param1);
var c2 = Expression.Constant(param2);
var expr = Expression.Call(typeof(MyClass).GetMethod("MyFunc"), c1, c2);
Func<int> func = Expression.Lambda<Func<int>>(expr).Compile();

Console.WriteLine(func.Invoke());
```

[Try it online](https://dotnetfiddle.net/538Qhi)