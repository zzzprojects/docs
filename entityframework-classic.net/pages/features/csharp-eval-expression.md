---
Name: C# Eval Expression
---

# C# Eval Expression

## Description
The **C# Eval Expression** features let you execute code dynamically.

```csharp
var result = Eval.Execute("X + Y", new { X = 1, Y = 2 }); // return 3
```
Try it: [NET Core](https://dotnetfiddle.net/SCrswb) | [NET Framework](https://dotnetfiddle.net/W9TwcP)

This feature is provided by the library [C# Eval Expression](https://eval-expression.net/) _(Included with EF Classic)_.

## Real-Life Scenarios

### Sets entity value from JSON
Your application needs to set property value from a JSON file you receive.

```csharp
var json = @"
[
    {
      'Type': 'Customer',
      'Properties': {
          'Code': 'Z0',
           'FirstName': 'ZZZ',
          'LastName': 'Projects'
      }
    },
    {
      'Type': 'Customer',
      'Properties': {
          'Code': 'Z1',
           'FirstName': 'Jonathan',
          'LastName': 'Magnan'
      }
    }
]
";

var listToMerge = new List<object>();
foreach(var item in list)
{
    var obj = Eval.Execute("new " + item.Type + "()");
    listToMerge.Add(obj);

    foreach(var property in item.Properties)
    {
        Eval.Execute(property.Name + " = '" + property.Value + "'", obj);
    }
}
```
Try it: [NET Core](https://dotnetfiddle.net/xPjYVr) | [NET Framework](https://dotnetfiddle.net/2sAvrj)

### Executing LINQ from JSON
Your application needs to filter returning entities depending on the JSON you receive.

```csharp
var json = @"
[
    {
      'Method': 'Where',
      'Field': 'Name',
      'Operation': '==',
      'Value': '""Customer_A""',
    },
    {
      'Method': 'Where',
      'Expression': 'Description.Contains(""ZZZ Projects"")',
    }
]
";

foreach(var qc in queryCriterias)
{
    if(qc.Method == "Where") 
    {
        if(!string.IsNullOrEmpty(qc.Expression))
        {
            query = query.Where(x => "x." + qc.Expression);
        }
        else
        {
            var expression = "x.[Field] [Operation] [Value]"
            .Replace("[Field]", qc.Field)
            .Replace("[Operation]", qc.Operation)
            .Replace("[Value]", qc.Value);
            
            query = query.Where(x => expression);
        }
    }
}
```
Try it: [NET Core](https://dotnetfiddle.net/5dp1qY) | [NET Framework](https://dotnetfiddle.net/UptHy0)

## Learn more

More documentation can be found here: [C# Eval Expression](https://eval-expression.net/)
