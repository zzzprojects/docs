---
Permalink: advanced-configuration
Name: Configuration
---

# Configuration

The `System.Linq.Dynamic.Core` library offers more functionalities and settings that are made optional via configuration.

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

In order to use the ParsingConfig, you will **need** to pass this config object as the **first** parameter.

## ParsingConfig

It is the configuration class in System.Linq.Dynamic.Core library and it provides the following settings.

### AllowNewToEvaluateAnyType

It allows the new keyword to evaluate any available Type. The default value is false.

### AreContextKeywordsEnabled

It determines if the context keywords (like it, parent, and root) are valid and usable inside a Dynamic LINQ string expression.

- All together, it does not affect the usability of the equivalent context symbols ($, ^, and ~).
- The default value is true.

### DateTimeIsParsedAsUTC

By default, DateTime (like `Fri, 10 May 2019 11:03:17 GMT`) is parsed as local time. Using this flag will parse all DateTime strings as UTC. The default value is false.

### DisableMemberAccessToIndexAccessorFallback

By default, when a member is not found in a type and the type has a string-based index accessor it will be parsed as an index accessor.

- Use this flag to disable this behavior and have parsing fail when parsing an expression where a member access on a non-existing member.
- The default value is false.

### EvaluateGroupByAtDatabase

It gets or sets a value indicating whether the Entity Framework version supports evaluating GroupBy at the database level. See also [linq-groupby-translation](https://docs.microsoft.com/en-us/ef/core/what-is-new/ef-core-2.1#linq-groupby-translation). The default value is false.

### NumberParseCulture

Set this if you want to use another number of parsing culture. The default value is InvariantCulture.

For example, if you set this to `CultureInfo.CreateSpecificCulture("de-DE")` and if you use a value like `3,21`, this is parsed to the decimal value `3.21`.

### RenameParameterExpression

It renames the (Typed) ParameterExpression empty Name to the correct supplied name from it. The default value is false.

So this string-expression:

```csharp
string predicate = "c => c.CompanyName == \"ABC\" && c.Location.Name == \"test\"";
```

By default, the generated expression will be translated into something like this (the `c` is lost and replaced by `Param_0`):

```csharp
Param_0 => ((Param_0.CompanyName == "ABC") AndAlso (Param_0.Location.Name == "test"))
```

When this configuration value is set to **true**, the expression will have this (the original `c` is preserved):

```csharp
c => ((c.CompanyName == "ABC") AndAlso (c.Location.Name == "test"))
```

See this [Try it online](https://dotnetfiddle.net/PeZbtN).

### ResolveTypesBySimpleName

By default, finding types by a simple name is not supported.

- Use this flag to use the CustomTypeProvider to resolve types by a simple name like "Employee" instead of "MyDatabase.Entities.Employee".
- Since the first matching type is returned, and this functionality needs to scan all types from all assemblies, it must be used with caution.
- The default value is false.

### SupportEnumerationsFromSystemNamespace

It provides support for enumeration-types from the System namespace in "mscorlib". For example, "StringComparison". The default value is true.

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

More info, see [entity-framework-dynamic-queries-and-parameterization](https://github.com/graeme-hill/gblog/blob/master/source_content/articles/2014.139_entity-framework-dynamic-queries-and-parameterization.mkd).

[Try it online](https://dotnetfiddle.net/CF6AQ8)

### RenameEmptyParameterExpressionNames

It prevents any System.Linq.Expressions.ParameterExpression-Name value from being empty by substituting a random 16 character word. The default value is false.

### CustomTypeProvider

The `CustomTypeProvider` allows you to implement your own types so that you can add more types to the accessible types.

For example, if you want to use **XElement** and **XAttribute** in your dynamic LINQ Query, create a custom type provider to add **XElement** and **XAttribute** to the accessible types.

```csharp
public class MyCustomTypeProvider : DefaultDynamicLinqCustomTypeProvider
{
    public override HashSet<Type> GetCustomTypes() =>
        new[] { typeof(XName), typeof(XElement), typeof(XAttribute) }.ToHashSet();
}
```

Now, you can assign your custom provider to the configuration and call your query as shown below.

```csharp
// Create a config object and pass the custom TypeProvider
var config = new ParsingConfig
{
    CustomTypeProvider = new MyCustomTypeProvider()
};

var IPName = (XName) "IP";
var OSName = (XName) "OS";

var query = new[]
{
    new { OS = "Windows", IPAddress = "127.0.0.1" },
    new { OS = "Linux",   IPAddress = "127.0.0.2" }
}

// Use the Select-method to create a XElement with a value from the OS, and an XAttribute with the value from the IP-Address
.AsQueryable().Select(config, "XElement(@0, new [] { OS, XAttribute(@1, IPAddress) })", OSName, IPName);
```

[Try this online](https://dotnetfiddle.net/jjQDSo)

### NullPropagatingUseDefaultValueForNonNullableValueTypes

When using the NullPropagating function np(...), use a "default value" for non-nullable value types instead of "null value". Default value for this option is false.

**Example class**

``` csharp
public class X
{
    public int Number { get; set; }
}
```

**When set to false (default)**
This code:
``` csharp
var result = new [] { new X() }.AsQueryable()
    .Select("np(it.Number)").First();
```

will "translate" into:
``` csharp
var result = new [] { new X() }.AsQueryable()
    .Select(x => x != null ? (int?) x.Number : null).First();
```
Where `result` is a `Nullable<int>` with value `0`.

**When set to true**

This code:
``` csharp
var config = new ParsingConfig
{
    NullPropagatingUseDefaultValueForNonNullableValueTypes = true
};
var result = new [] { new X() }.AsQueryable()
    .Select(config, "np(it.Number)").First();
```

Will "translate" into:
``` csharp
var result = new [] { new X() }.AsQueryable()
    .Select(x => x != null ? x.Number : default(int)).First();
```
Where `result` is a `int` with value `0`.

### SupportCastingToFullyQualifiedTypeAsString

Support casting to a full qualified type using a string (double quoted value). Default value is `true`.

Example:
``` csharp
public class Entry
{
    public object LastUpdate { get; set; }
}

var data = new [] { new Entry { LastUpdate = DateTime.Now  } };
var queryable = data.AsQueryable());
var result = queryable.Select($"\"System.DateTime\"(LastUpdate)");
```

It's also possible to cast to a nullable type:
``` csharp
public class Entry
{
    public object? OptionalDate { get; set; }
}

var data = new [] { new Entry { OptionalDate = DateTime.Now  } };
var queryable = data.AsQueryable());
var result = queryable.Select($"\"System.DateTime\"?(OptionalDate)");
```

### PrioritizePropertyOrFieldOverTheType

When the type and property have the same name the parser takes the property instead of type when this setting is set to `true`.

#### Example
``` csharp
public class Company
{
    public DateTime DateTime { get; set; }
}
```

The ParsingConfig should be like:
``` csharp
var config = new ParsingConfig
{
    PrioritizePropertyOrFieldOverTheType = true
};
```

Now the following dynamic query can be used:
``` csharp
var companies = new List<Company>().AsQueryable();

var result = companies.Where(config, "DateTime > \"2023-01-1\"").ToArray();
```

#### Note 1
The value from this setting should also be set to `true` when calling extension methods. 

#### Note 2
- This setting was introduced in version `1.2.25`, with the default value set to `false`.
- From version `1.3.0` and higher, the default value is set to `true`.

### SupportDotInPropertyNames (since 1.3.3)

Support a `.` in a property-name. Default value is `false`.

``` csharp
var config = new ParsingConfig
{
    SupportDotInPropertyNames = true
};

var result = persons.Select(config, "new (Profile.FirstName as Prof.Name)").ToDynamicArray();
```

### DisallowNewKeyword (since v1.3.5)

Disallows the `new` keyword to be used to construct a class.

``` csharp
var config = new ParsingConfig
{
    DisallowNewKeyword = true
};
```

### LoadAdditionalAssembliesFromCurrentDomainBaseDirectory (since v1.4.0)

This is a breaking change which was introduced in version `1.4.0`.

When using the full .NET Framework or .NET Core App 2.x and higher, it is possible to load additional assemblies from the current domain base directory. However, this is disabled by default because it can be very slow when all assemblies are scanned and loaded. If you still want to enable it, set the value to `true`. 


``` csharp
var config = new ParsingConfig
{
	LoadAdditionalAssembliesFromCurrentDomainBaseDirectory = true
};
```

### StringLiteralParsingType (since v1.4.3)

This configuration setting defines the type of string literal parsing that will be performed. The `StringLiteralParsingType` is an enum with two possible values:

#### `Default`
Represents the default string literal parsing type. Double quotes should be escaped using the default escape character (a `\`).

To check if a Value equals a double quote (`"`), use this c# code:
``` csharp
var result = customers.Where("Value == \"\\\"\"");
```


#### `EscapeDoubleQuoteByTwoDoubleQuotes`
Represents a string literal parsing type where a double quote should be escaped by an extra double quote (`"`).

To check if a Value equals a double quote (`"`), use this c# code:
``` csharp
var config = new ParsingConfig
{
	StringLiteralParsingType = StringLiteralParsingType.EscapeDoubleQuoteByTwoDoubleQuotes
};
var result = customers.Where(config, "Value == \"\"\"\"");
```