# TypeAs

You can build an expression tree which contains an explicit reference or boxing conversion using `Expression.TypeAs` method. For example, you have the following code.

```csharp
Employee e = new Employee();
object objEmp = e as object;
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
UnaryExpression typeAsExpression =
    Expression.TypeAs(
        Expression.Constant(e, typeof(Employee)),typeof(object));

Console.WriteLine(typeAsExpression.ToString());
```

[Try it online](https://dotnetfiddle.net/VzQT3Y)