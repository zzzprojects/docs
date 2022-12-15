---
Name: Eval Expression Options
---

# How to use options with C# Eval Expression library

Developers love our C# Expression Evaluator because it is easy to use, very flexible, and our team is always there to support your needs! You have a requirement that we still don't cover; you only have to [report it](https://github.com/zzzprojects/Eval-Expression.NET/issues), and our team will either add this behavior by default or create an option that will cover your scenario.

First, let's see all available options, and then we will explain them one by one:

| Category | Name                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| -------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Options  | AllowAddSubtractOperatorToCollection | The AllowAddSubtractOperatorToCollection option lets you get or set if the minus characters `-` can be used to remove an item from a list. In other words, doing `list - 200` will behave like `list.Remove(200)`. By default, the AllowAddSubtractOperatorToCollection value is `false`.                                                                                                                                                                                                                                          |
| Options  | AutoAddMissingTypes                  | The AutoAddMissingTypes option lets you get or set if the library should try to automatically find and add missing types when an expression cannot compile due to missing types. You can specify which assembly the missing types should look for through the RegisterAutoAddMissingTypeAssembly method, otherwise the library will search over all domain assemblies. By default, the AutoAddMissingTypes value is false for performance and side impact reasons.                                                                   |
| Options  | BindingFlags                         | The BindingFlags option lets you get or set the flags when searching for a member (Property, Field, Method). By default, the BindingFlags value is \`BindingFlags.Public &#124; BindingFlags.NonPublic &#124; BindingFlags.FlattenHierarchy &#124; BindingFlags.IgnoreCase\`.                                                                                                                                                                                                                                                                       |
| Options  | CacheKeyPrefix                       | The CacheKeyPrefix option lets you get or set the prefix in the Cache Key. That option can be helpful if you want to make the cache key more unique. By default, the CacheKeyPrefix value is \`\[evalContext\].GetType().FullName\` (The full name of the EvalContext type).                                                                                                                                                                                                                                                         |
| Options  | DefaultNumberType                    | The DefaultNumberType option lets you get or get what type a number without a data type suffixes should be. For example, if you set the value to \`DefaultNumberType.Double\`, the expression "1+2" will consider the \`1\` and \`2\` numbers as a double instead of an \`int\` and will return a \`double\`. By default, the DefaultNumberType value is \`DefaultNumberType.None\`, which will act like the default C# behavior with numbers.                                                                                       |
| Options  | DisableAutoRegisterEntityFramework   | The DisableAutoRegisterEntityFramework option lets you get or sets if the auto registration from some method when using Entity Framework Core and EF6 should be disabled. By default, the DisableAutoRegisterEntityFramework value is false.                                                                                                                                                                                                                                                                                         |
| Options  | DisableAutoReplaceDictionaryKey      | The DisableAutoReplaceDictionaryKey option lets you get or sets if the feature that allows using the dictionary keys directly in the expression should be disabled. When true, you will still be able to access the dictionary but no longer be able to use directly dictionary key. By default, the DisableAutoReplaceDictionaryKey value is false.                                                                                                                                                                                 |
| Options  | DisableDynamicResolution             | The DisableDynamicResolution option lets you get or sets all logic to solve some issue when the expression is of type dynamic  should be disabled. For example, when a binary operation is used between an ExpandoObject property and an int is used. By default, the DisableDynamicResolution value is false.                                                                                                                                                                                                                       |
| Options  | DynamicGetMemberMissingValueFactory  | The DynamicGetMemberMissingValueFactory option lets you get or sets a factory to return a value when a member is not found. In other words, it allows specifying a value for a missing member on demand. By default, the DynamicGetMemberMissingValueFactory value is null.                                                                                                                                                                                                                                                          |
| Options  | DynamicMemberNames                   | The DynamicMemberNames option lets you when compiling an expression using an ExpandoObject as a parameter, retrieve all member names that we assume will be a member of the ExpandoObject. For example, suppose the \`CustomerID\` is returning from the member names list, in that case, the library expects the ExpandoObject will have a property and a value for this \`CustomerID\` to be executed correctly. By default, the DynamicMemberNames value is null.                                                                 |
| Options  | ForceCharAsString                    | The ForceCharAsString option lets you get or set if a \`char\` such as \`'z'\` must be forced as a string as if you would have been specified \`"z"\`. The library is smart enough to know that the expression 'zzz' is a string (more than one char) but sometimes cannot know if 'z' is a string or a char, in particular when a method exists with an overload for a \`char\` and a \`string\`. By default, the ForceCharAsString value is false.                                                                                 |
| Options  | ForceObjectAsDynamic                 | The ForceObjectAsDynamic option lets you get or set if the type \`object\` should act like the type \`ExpandoObject\` on some occasions, such as when using a binary operation. By default, the ForceObjectAsDynamic value is false.                                                                                                                                                                                                                                                                                                 |
| Options  | IncludeMemberFromAllParameter        | The IncludeMemberFromAllParameter option lets you get or set if we should include all members from all parameters, like the default logic, automatically does when only one parameter is specified. So, for example, if you specify a customer and an invoice, you can use the \`CustomerID\` and \`InvoiceID\` properties directly. If a member name exists in more than one parameter, such as \`ID\`, the first member will be used, and all others be ignored. By default, the IncludeMemberFromAllParameter value is \`false\`. |
| Options  | IsCaseSensitive                      | The IsCaseSensitive option lets you get or set the flags when searching for a member (Property, Field, Method) if the search should be case sensitive. Under the hood, the \`IgnoreCase\` binding flag is added or removed to the BindingFlags options. By default, the IsCaseSensitive value is true or the value assigned to the BindingFlags options if already modified.                                                                                                                                                         |
| Options  | MaxLoopIteration                     | The MaxLoopIteration option lets you get or set the maximum of iteration allowed for a loop (do, while, for, foreach). For example, if you execute an expression from a user input but want to avoid an infinite loop, you can set the option \`MaxLoopIteration = 25\` to limit it to 25 iterations. By default, the MaxLoopIteration value is null (unlimited).                                                                                                                                                                    |
| Options  | MemoryCacheEntryOptionsFactory       | The MemoryCacheEntryOptionsFactory option lets you get or set for .NET 5+ and .NET Core the cache entry options to use when caching the delegate returned from the compile method. By default, the MemoryCacheEntryOptionsFactory value is \`new MemoryCacheEntryOptions() { Size = 1 };\`.                                                                                                                                                                                                                                          |
| Options  | MissingTypes                         | The MissingType option lets you get or set the missing types to make an expression valid. The list is automatically populated when the option \`AutoAddMissingTypes\` or \`RetryAndThrowMissingTypes\` is used. By default, the MissingType value is null.                                                                                                                                                                                                                                                                           |
| Options  | RetryAndThrowMissingTypes            | The RetryAndThrowMissingTypes option lets you get or set if an error should be thrown by trying again to compile the expression and populate the \`MissingTypes\` list with types missing to make an expression valid. To compile an expression by automatically adding missing types, use the \`AutoAddMissingTypes\` option instead. By default, the RetryAndThrowMissingTypes is false.                                                                                                                                           |
| Options  | SafeMode                             | The SafeMode option lets you get or set if only members and types registered can be used for the expression. In other words, you can safely execute user input in your environment by restricting him to use only what you allow. Make sure to read the \`SafeMode\` documentation if you plan to use this option. By default, the SafeMode value is false.                                                                                                                                                                          |
| Options  | UseCache                             | The UseCache option lets you get or set if the cache should be used to retrieve or cache the delegate created from the compile method. By default, the UseCache value is true.                                                                                                                                                                                                                                                                                                                                                       |
| Options  | UseCaretForExponent                  | The UseCaretForExponent option lets you get or set if the caret character \`^\` should behave as an exponent operator instead of an XOR (logical exclusive OR) operator. For example, you want the following expression, \`2^3\` to return 8 (2 to the power of 3). The double caret \`^^\` (\`2^^3\`) already acts as an exponent operator whether this option is enabled or not. By default, the UseCaretForExponent value is false.                                                                                               |
| Options  | UseEqualsAssignmentAsEqualsOperator  | The UseEqualsAssignmentAsEqualsOperator option lets you get or set if the equal assignation \`=\` should instead act like an equal operator. For example, you evaluate some math formula and would want the expression \`2+2=4\` instead of trying to make a variable assignment. By default, the UseEqualsAssignmentAsEqualsOperator value is false.                                                                                                                                                                                |
| Options  | UseLocalCache                        | The UseLocalCache option lets you get or set if the global cache (for all eval context) or a local cache (for this eval context) should be used when we retrieve or cache the delegate created from the compile method. By default, the UseLocalCache value is false.                                                                                                                                                                                                                                                                |
| Options  | UseShortCacheKey                     | The UseShortCacheKey option lets you get or set if the cache key should be shortened. Enabling that option can increase performance, but ensure to read the full \`UseShortCacheKey \` documentation before using it. By default, the UseShortCacheKey value is false.                                                                                                                                                                                                                                                               |
| Options  | UseSmartExecuteParameterResolution   | The UseSmartExecuteParameterResolution option lets you get or set if the smart resolution should be used when passing multiple values to the execute method, like the default logic, which automatically does when only one parameter is specified. This option is very similar to the option \`IncludeMemberFromAllParameter\` but is different in the way that we also register value to know the type already. By default, the IncludeMemberFromAllParameter value is false.                                                      |
| Options  | UseSmartTypeResolution               | The UseSmartTypeResolution option lets you get or set if some type should be found automatically without specifying the \`System\` namespace. For example, when enabled, you can automatically use \`Math.Min\`, but when disabled, you need to use\`System.Math.Min\`. By default, the UseSmartTypeResolution value is true.                                                                                                                                                                                                        |
| Options  | UseTypeBeforeDynamic                 | The UseTypeBeforeDynamic option lets you get or set if, for some expression, the logic using the type should have priority over the resolution using dynamic. By default, the UseTypeBeforeDynamicvalue is false.                                                                                                                                                                                                                                                                                                                    |
| Options  | VariableFactory                      | The VariableFactory option lets you get or sets a factory to return a value when a variable is not found. In other words, it allows specifying a value for a variable on demand. By default, the VariableFactory value is null.                                                                                                                                                                                                                                                                                                      |

> NOTE: In our example, we will configure an instance of the `EvalContext`, but you can set all options as well in the global context.

## AllowAddSubtractOperatorToCollection

The AllowAddSubtractOperatorToCollection option lets you get or set if the minus characters `-` can be used to remove an item from a list. In other words, doing `list - 200` will behave like `list.Remove(200)`. By default, the AllowAddSubtractOperatorToCollection value is `false`. 

In this example, we will add an item to a list and remove it. We will do it for a list of `int`, a list of `string`, and a list of `entity`.

```csharp
// Global Context: EvalManager.DefaultContext.AllowAddSubtractOperatorToCollection = true;

var context = new EvalContext();
context.AllowAddSubtractOperatorToCollection = true;

// using Add and Substract with a list of `int`
{
	var list = new List<int>();
	context.Execute("list + 1", new { list });	
	context.Execute("list - 1", new { list });
}

// using Add and Substract with a list of `string`
{
	var list = new List<string>();
	context.Execute("list + 'C# Eval Expression'", new { list });	
	context.Execute("list - 'C# Eval Expression'", new { list });
}

// using Add and Substract with a list of `Entity`
{
	var list = new List<Entity>();
	var entity = new Entity();
	context.Execute("list + entity", new { list, entity });	
	context.Execute("list - entity", new { list, entity });
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/UUw6fX' %}

## AutoAddMissingTypes

The AutoAddMissingTypes option lets you get or set if the library should try to automatically find and add missing types when an expression cannot compile due to missing types. You can specify which assembly the missing types should look for through the RegisterAutoAddMissingTypeAssembly method, otherwise the library will search over all domain assemblies. By default, the AutoAddMissingTypes value is `false` for performance and side impact reasons.

In this example, we will try to evaluate an expression the first time without the `AutoAddMissingTypes` option, which will fail, and then try again but with the option this time.

```csharp
// Global Context: EvalManager.DefaultContext.AutoAddMissingTypes = true;

var context = new EvalContext();

try
{
	var fail = (Entity)context.Execute("return new Entity() { EntityID = 13 }");
}
catch(Exception ex)
{
	Console.WriteLine("1 - Exception: " + ex.Message);
}

// Using the `AutoAddMissingTypes`
{
	context.AutoAddMissingTypes = true;
	var entity = (Entity)context.Execute("return new Entity() { EntityID = 13 }");

	Console.WriteLine("2 - EntityID: " + entity.EntityID);
}

// NOTE: The library has a built-in smart resolution when a type is already passing in a parameter or as a return type
{
	var context2 = new EvalContext();
	
	var entity3 = (Entity)context2.Execute("return new Entity() { EntityID = 13 }", new { AnotherEntity = new Entity() });
	Console.WriteLine("3 - EntityID: " + entity3.EntityID);
	
	var entity4 = context2.Execute<Entity>("return new Entity() { EntityID = 13 }");
	Console.WriteLine("3 - EntityID: " + entity4.EntityID);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/eXzV65' %}

> NOTE: We also added two examples of our built-in smart resolution allowing our library to discover types from the parameter and return type without using the `AutoAddMissingTypes` option.

## BindingFlags

The BindingFlags option lets you get or set the flags when searching for a member (Property, Field, Method). By default, the BindingFlags value is `BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.FlattenHierarchy | BindingFlags.IgnoreCase`.

In this example, we will first evaluate an expression by accessing a property with the wrong casing, which will succeed as the library is case insensitive by default. Then, we will remove the `IgnoreCase` flag and try again to access the property value, which will raise an error.

```csharp
// Global Context: EvalManager.DefaultContext.BindingFlags = BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.FlattenHierarchy;

var context = new EvalContext();
var entity = new Entity() { EntityID = 13 };

var entityID = context.Execute("entity.enTItyiD", new { entity });

try
{
	context.BindingFlags = BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.FlattenHierarchy;
	// or: context.BindingFlags &= ~BindingFlags.IgnoreCase;
	
	var fail = context.Execute("entity.enTItyiD", new { entity });
}
catch(Exception ex)
{
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/iRszDn' %}

## CacheKeyPrefix

The CacheKeyPrefix option lets you get or set the prefix in the Cache Key. That option can be helpful if you want to make the cache key more unique. By default, the CacheKeyPrefix value is `[evalContext].GetType().FullName` (The full name of the EvalContext type). 

In this example, we will register a static method and call our context `Execute` method. Then we will unregister this static method and use the expression again, and we will see the `Execute` is still working. That is still working because the library does not compile the expression but instead takes the delegate previously created directly from the cache. So to reset the cache, we will set another `CacheKeyPrefix` and try again to execute our expression, which will correctly fail this time.

```csharp
// Global Context: EvalManager.DefaultContext.CacheKeyPrefix = "Another Cache Key";

var context = new EvalContext();

context.RegisterStaticMethod(typeof(Program));

var r1 = context.Execute("AddMe(1, 2)");

context.UnregisterStaticMethod(typeof(Program));

// Oops! Even if unregistering the `AddMe` method, the expression still succeeds as we take the delegate from the cache
var r2 = context.Execute("AddMe(1, 2)");

try
{
	// By changing the cache key, we somewhat reset the cache so the expression will not compile anymore
	context.CacheKeyPrefix = "Another Cache Key";
	var fail = context.Execute("AddMe(1, 2)");
}
catch(Exception ex)
{
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/KjgXIb' %}

## DefaultNumberType

The DefaultNumberType option lets you get or get what type a number without a data type suffixes should be. For example, if you set the value to `DefaultNumberType.Double`, the expression `1+2` will consider the `1` and `2` numbers as a double instead of an `int` and will return a `double`. By default, the DefaultNumberType value is `DefaultNumberType.None`, which will act like the default C# behavior with numbers.

In this example, we will prove that `1/2=0` by directly using a C# statement than using an expression within our library. Then, we will set the `DefaultNumberType.Double` to check the difference in the result.

```csharp
// Global Context: EvalManager.DefaultContext.DefaultNumberType = DefaultNumberType.Double;

var context = new EvalContext();

// Let proof that 1 divided by 2 equal 0 in C#
var r1 = 1/2; // return 0

// Let check if C# eval have the same behavior
var r2 = context.Execute("1/2"); // return 0

// Let try again with the DefaultNumberType
context.DefaultNumberType = DefaultNumberType.Double;
var r3 = context.Execute("1/2"); // return 0.5
```

{% include component-try-it.html href='https://dotnetfiddle.net/ZvibpP' %}

## DisableAutoRegisterEntityFramework

```csharp
```

## DisableAutoReplaceDictionaryKey

```csharp
```

## DisableDynamicResolution

```csharp
```

## DynamicGetMemberMissingValueFactory

```csharp
```

## DynamicMemberNames

```csharp
```

## ForceCharAsString

```csharp
```

## ForceObjectAsDynamic

```csharp
```

## IncludeMemberFromAllParameter

```csharp
```

## IsCaseSensitive

```csharp
```

## MaxLoopIteration

```csharp
```

## MemoryCacheEntryOptionsFactory

```csharp
```

## MissingTypes

```csharp
```

## RetryAndThrowMissingTypes

```csharp
```

## SafeMode

```csharp
```

## UseCache

```csharp
```

## UseCaretForExponent

```csharp
```

## UseEqualsAssignmentAsEqualsOperator

```csharp
```

## UseLocalCache

```csharp
```

## UseShortCacheKey

```csharp
```

## UseSmartExecuteParameterResolution

```csharp
```

## UseSmartTypeResolution

```csharp
```

## UseTypeBeforeDynamic

```csharp
```

## VariableFactory

```csharp
```

---

## BindingFlags
Gets or sets the value of the BindingFlags to use to retrieve member (Constructor, Method, Property, and Field)

### Example
```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext();

// Make member case insensitive (Math.pOW = Math.Pow)
context.BindingFlags = BindingFlags.IgnoreCase | context.BindingFlags
```

## CacheKeyPrefix
Gets or sets the cache key prefix to use to cache compiled delegate in the memory.

> We always recommend to use a different cache key prefix if you modify the options.

### Example
```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext();
context.CacheKey = Guid.NewGuid().ToString();
```

## IncludeMemberFromAllParameters
Gets or sets if all members should be included from all parameters.

By default, members are only included when one parameter is specified.

> This configuration can be very useful to disable if you execute multiple expressions only once, and you believe they will never be reused.

### Example
```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext();
context.IncludeMemberFromAllParameters = true;

Eval.Execute<bool>("catPropertyName == dogPropertyName", cat, dog);
```

## UseCache
Gets or sets if compiled delegate shoud be cached in the memory or not. 

> This configuration can be very useful to disable if you execute multiple expressions only once, and you believe they will never be reused.

### Example
```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext();
context.UseCache = false;
```

## UseCaretForExponent
Gets or sets if the default behavior for the XOR operator '^' must act like an exponent operator instead.

> A new operator '^|' is available for the bitwise exclusive-OR operator.

### Example
```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext() { UseCache = false };

var exclusiveOrValue = context.Eval("2^3"); // return 1;

context.UseCaretForExponent = true;
var exponentValue = = context.Eval("2^3"); // return 8;

var newExlusiveOrValue = = context.Eval("2^|3"); // return 1;
```

## Clone
Create a new shallow copy of the current EvalContext.

> Cloning the EvalContext can be useful to copy all options including all types already registered to use it to change only what's required for the expression to evaluate.

### Example
```csharp
// using Z.Expressions; // Don't forget to include this.
var context = EvalManager.DefaultContext.Clone();
```
