---
PermaID: 101003
Name: Configuration
---

# Configuration

The `System.Linq.Dynamic.Core` library offers more functionality which is made optional via configuration.

```csharp
// Create a config object
var config = new ParsingConfig
{
    UseParameterizedNamesInDynamicQuery = true
};

// Pass the config object as first parameter in the IQueryable extension methods, in this example Where(...)
using (var context = new EntityContext())
{    
    var query = context.Customers.Where(config, "City = @0", "Paris");
};
```

## ParsingConfig

It is the configuration class in System.Linq.Dynamic.Core library provides the following settings.

### AllowNewToEvaluateAnyType

It allows the new keyword to evaluate any available Type. The default value is false.

### AreContextKeywordsEnabled

It determines if the context keywords (like it, parent, and root) are valid and usable inside a Dynamic LINQ string expression. 
 - At the same time, it does not affect the usability of the equivalent context symbols ($, ^, and ~). 
 - The default value is true.

### DateTimeIsParsedAsUTC

By default, DateTime (like 'Fri, 10 May 2019 11:03:17 GMT') is parsed as local time. Using this flag will parse all DateTime strings as UTC. The default value is false.

### DisableMemberAccessToIndexAccessorFallback 

By default, when a member is not found in a type and the type has a string-based index accessor it will be parsed as an index accessor.

 - Use this flag to disable this behavior and have parsing fail when parsing an expression where a member access on a non-existing member. 
 - The default value is false.

### EvaluateGroupByAtDatabase 

It gets or sets a value indicating whether the Entity Framework version supports evaluating GroupBy at the database level. The default value is false. 

### RenameParameterExpression 

It renames the (Typed) ParameterExpression empty Name to the correct supplied name from it. The default value is false.

### ResolveTypesBySimpleName 

By default, finding types by a simple name is not supported.

 - Use this flag to use the CustomTypeProvider to resolve types by a simple name like "Employee" instead of "MyDatabase.Entities.Employee". 
 - The first matching type is returned, and this functionality needs to scan all types from all assemblies, so use with caution. 
 - The default value is false.

### SupportEnumerationsFromSystemNamespace 

It provides support for enumeration-types from the System namespace in “mscorlib”. For example, "StringComparison". The default value is true.

### UseParameterizedNamesInDynamicQuery 

It uses Parameterized Names in generated dynamic SQL query. The default value is false. 

```csharp
// Create a config object
var config = new ParsingConfig
{
    UseParameterizedNamesInDynamicQuery = true
};

// Pass the config object as first parameter in the IQueryable extension methods, in this example Where(...)
using (var context = new EntityContext())
{    
    var query = context.Customers.Where(config, "City = @0", "Paris");
};
```

[Try it online](https://dotnetfiddle.net/CF6AQ8)

### RenameEmptyParameterExpressionNames 

It prevents any System.Linq.Expressions.ParameterExpression.Name value from being empty by substituting a random 16 character word. The default value is false.

### CustomTypeProvider 

The `CustomTypeProvider` allows you to implement your own types so that you can add more types to the accessible types.

For example, if you want to use **XName** and **XElement** in your dynamic LINQ Query, create a custom type provider to add **XName** and **XElement** to the accessible types.

```csharp
public class MyCustomTypeProvider : DefaultDynamicLinqCustomTypeProvider
{
    public override HashSet<Type> GetCustomTypes() => 
        new[] { typeof(XName), typeof(XElement) }.ToHashSet();
}
```

Now you can assign your custom provider to the configuration and call your query as shown below.

```csharp
// Create a config object
var config = new ParsingConfig
{
    CustomTypeProvider = new MyCustomTypeProvider()
};

var OSName = (XName)"OS";
var query = XmlSource.Elements().AsQueryable()
    .Select(config, "new (Attribute(XName.Get(\"IP\")).Value as IP, Element(@0).Value as OS)", OSName);
```
