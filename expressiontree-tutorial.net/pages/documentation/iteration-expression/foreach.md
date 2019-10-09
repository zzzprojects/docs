# Foreach

You can build an expression tree to perform a foreach loop by using `Expression.Loop` method. For example, you have the following code.

```csharp
var list = new List<int>() { 10, 20, 30 };

foreach (var loopVar in list)
{
    Console.WriteLine(loopVar);
}
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
public static Expression ForEach(Expression collection, ParameterExpression loopVar, Expression loopContent)
{
    var elementType = loopVar.Type;
    var enumerableType = typeof(IEnumerable<>).MakeGenericType(elementType);
    var enumeratorType = typeof(IEnumerator<>).MakeGenericType(elementType);

    var enumeratorVar = Expression.Variable(enumeratorType, "enumerator");
    var getEnumeratorCall = Expression.Call(collection, enumerableType.GetMethod("GetEnumerator"));
    var enumeratorAssign = Expression.Assign(enumeratorVar, getEnumeratorCall);

    var moveNextCall = Expression.Call(enumeratorVar, typeof(IEnumerator).GetMethod("MoveNext"));

    var breakLabel = Expression.Label("LoopBreak");

    var ifThenElseExpr = Expression.IfThenElse(
        Expression.Equal(moveNextCall, Expression.Constant(true)),
        Expression.Block(new[] { loopVar },
            Expression.Assign(loopVar, Expression.Property(enumeratorVar, "Current")),
            loopContent
        ),
        Expression.Break(breakLabel)
    );

    var loop = Expression.Loop(ifThenElseExpr, breakLabel);

    var block = Expression.Block(new[] { enumeratorVar },
        enumeratorAssign,
        loop
    );

    return block;
}
```

This function can be easily used as shown below.

```csharp
var list = new List<int>() { 10, 20, 30 };
var collection = Expression.Parameter(typeof(List<int>), "collection");
var loopVar = Expression.Parameter(typeof(int), "loopVar");
var loopBody = Expression.Call(typeof(Console).GetMethod("WriteLine", new[] { typeof(int) }), loopVar);
var loop = ForEach(collection, loopVar, loopBody);
var results = Expression.Lambda<Action<List<int>>>(loop, collection).Compile();
results(list);
```

[Try it online](https://dotnetfiddle.net/Pl89Gr)