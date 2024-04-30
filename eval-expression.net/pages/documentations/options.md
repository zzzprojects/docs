---
Name: Eval Expression Options
LastMod: 2024-03-22
---

# How to use options with C# Eval Expression library

Developers love our C# Expression Evaluator because it is easy to use, very flexible, and our team is always there to support your needs! You have a requirement that we still don't cover; you only have to [report it](https://github.com/zzzprojects/Eval-Expression.NET/issues), and our team will either add this behavior by default or create an option that will cover your scenario.

First, let's see all available options, and then we will explain them one by one:

| Category | Name                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| -------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Options  | AllowAddSubtractOperatorToCollection | The AllowAddSubtractOperatorToCollection option lets you get or set if the minus characters `-` can be used to remove an item from a list. In other words, doing `list - 200` will behave like `list.Remove(200)`. By default, the AllowAddSubtractOperatorToCollection value is `false`.                                                                                                                                                                                                                                            |
| Options  | AutoAddMissingTypes                  | The AutoAddMissingTypes option lets you get or set if the library should try to automatically find and add missing types when an expression cannot compile due to missing types. You can specify which assembly the missing types should look for through the RegisterAutoAddMissingTypeAssembly method, otherwise the library will search over all domain assemblies. By default, the AutoAddMissingTypes value is false for performance and side impact reasons.                                                                   |
| Options  | BindingFlags                         | The BindingFlags option lets you get or set the flags when searching for a member (Property, Field, Method). By default, the BindingFlags value is \`BindingFlags.Public &#124; BindingFlags.NonPublic &#124; BindingFlags.FlattenHierarchy &#124; BindingFlags.IgnoreCase\`.                                                                                                                                                                                                                                                        |
| Options  | CacheKeyPrefix                       | The CacheKeyPrefix option lets you get or set the prefix in the Cache Key. That option can be helpful if you want to make the cache key more unique. By default, the CacheKeyPrefix value is \`\[evalContext\].GetType().FullName\` (The full name of the EvalContext type).                                                                                                                                                                                                                                                         |
| Options  | DefaultNumberType                    | The DefaultNumberType option lets you get or get what type a number without a data type suffixes should be. For example, if you set the value to \`DefaultNumberType.Double\`, the expression "1+2" will consider the \`1\` and \`2\` numbers as a double instead of an \`int\` and will return a \`double\`. By default, the DefaultNumberType value is \`DefaultNumberType.None\`, which will act like the default C# behavior with numbers.                                                                                       |
| Options  | DisableAutoRegisterEntityFramework   | The DisableAutoRegisterEntityFramework option lets you get or sets if the auto registration from some method when using Entity Framework Core and EF6 should be disabled. By default, the DisableAutoRegisterEntityFramework value is false.                                                                                                                                                                                                                                                                                         |
| Options  | DisableAutoReplaceDictionaryKey      | The DisableAutoReplaceDictionaryKey option lets you get or sets if the feature that allows using the dictionary keys directly in the expression should be disabled. When true, you will still be able to access the dictionary but no longer be able to use directly dictionary key. By default, the DisableAutoReplaceDictionaryKey value is false.                                                                                                                                                                                 |
| Options  | DisableConstantFolding      		  | The DisableConstantFolding option lets you to get or set if constant folding should be disabled. Constant folding is the logic to replace an equation with 2 constants like `2+3` directly into the constant `5`. This avoids running the addition every time the compiled method is executed.                                                                                                                                                                            															 |
| Options  | DisableDynamicResolution             | The DisableDynamicResolution option lets you get or sets all logic to solve some issue when the expression is of type dynamic  should be disabled. For example, when a binary operation is used between an ExpandoObject property and an int is used. By default, the DisableDynamicResolution value is false.                                                                                                                                                                                                                       |
| Options  | DynamicGetMemberMissingValueFactory  | The DynamicGetMemberMissingValueFactory option lets you get or sets a factory to return a value when a member is not found. In other words, it allows specifying a value for a missing member on demand. By default, the DynamicGetMemberMissingValueFactory value is null.                                                                                                                                                                                                                                                          |
| Options  | DynamicMemberNames                   | The DynamicMemberNames option lets you when compiling an expression using an ExpandoObject as a parameter, retrieve all member names that we assume will be a member of the ExpandoObject. For example, suppose the \`CustomerID\` is returning from the member names list, in that case, the library expects the ExpandoObject will have a property and a value for this \`CustomerID\` to be executed correctly. By default, the DynamicMemberNames value is null.                                                                 |
| Options  | ForceCharAsString                    | The ForceCharAsString option lets you get or set if a \`char\` such as \`'z'\` must be forced as a string as if you would have been specified \`"z"\`. The library is smart enough to know that the expression 'zzz' is a string (more than one char) but sometimes cannot know if 'z' is a string or a char, in particular when a method exists with an overload for a \`char\` and a \`string\`. By default, the ForceCharAsString value is false.                                                                                 |
| Options  | ForceIncludeInstanceMethodFromParameter | The ForceIncludeInstanceMethodFromParameter  option lets you get or set if instance method (found from parameter) should always be included. They are usually included only if no matching method are found.                                                                                 |
| Options  | ForceObjectAsDynamic                 | The ForceObjectAsDynamic option lets you get or set if the type \`object\` should act like the type \`ExpandoObject\` on some occasions, such as when using a binary operation. By default, the ForceObjectAsDynamic value is false.                                                                                                                                                                                                                                                                                                 |
| Options  | IncludeMemberFromAllParameter        | The IncludeMemberFromAllParameter option lets you get or set if we should include all members from all parameters, like the default logic, automatically does when only one parameter is specified. So, for example, if you specify a customer and an invoice, you can use the \`CustomerID\` and \`InvoiceID\` properties directly. If a member name exists in more than one parameter, such as \`ID\`, the first member will be used, and all others be ignored. By default, the IncludeMemberFromAllParameter value is \`false\`. |
| Options  | IsCaseSensitive                      | The IsCaseSensitive option lets you get or set the flags when searching for a member (Property, Field, Method) if the search should be case sensitive. Under the hood, the \`IgnoreCase\` binding flag is added or removed to the BindingFlags options. By default, the IsCaseSensitive value is true or the value assigned to the BindingFlags options if already modified.                                                                                                                                                         |
| Options  | LastCompiledExpression               | The LastCompiledExpression option lets you to get the expression that was used in the last compilation. This option is not thread safe, so it should always be used in a new instance of an eval context if your application is multi-threaded.                                                                                                                                                       																																 |
| Options  | LastCompiledParameterExpressions     | The LastCompiledParameterExpressions option lets you to get the list of parameter expression that was used in the last compilation. This option is not thread safe, so it should always be used in a new instance of an eval context if your application is multi-threaded..                                                                                                                                                         																								 |
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

var r1 = context.Execute<int>("AddMe(1, 2)");

context.UnregisterStaticMethod(typeof(Program));

// Oops! Even if unregistering the `AddMe` method, the expression still succeeds as we take the delegate from the cache
var r2 = context.Execute<int>("AddMe(1, 2)");

try
{
	// By changing the cache key, we somewhat reset the cache so the expression will not compile anymore
	context.CacheKeyPrefix = "Another Cache Key";
	var fail = context.Execute<int>("AddMe(1, 2)");
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
var r2 = context.Execute<double>("1/2"); // return 0

// Let try again with the DefaultNumberType
context.DefaultNumberType = DefaultNumberType.Double;
var r3 = context.Execute<double>("1/2"); // return 0.5
```

{% include component-try-it.html href='https://dotnetfiddle.net/ZvibpP' %}

## DisableAutoRegisterEntityFramework

The DisableAutoRegisterEntityFramework option lets you get or set if the auto registration of some method of Entity Framework Core and EF6, such as being able to use `DbFunctions` and `DbFunctionExtensions` with Dynamic LINQ should be disabled. By default, the DisableAutoRegisterEntityFramework value is `false`.

In this example, we will query the customers table by filtering it using the `EF.Functions.DateDiffMonth` method and shows all returned customers. By default, the query will work successfully, but if you uncomment the `DisableAutoRegisterEntityFramework = true` option, the query will no longer work and throw an error.

```csharp
// Uncomment to make the next query fail
// EvalManager.DefaultContext.DisableAutoRegisterEntityFramework = true;

try
{
	using (var context = new EntityContext())
	{
		
		var customers = context.Customers.WhereDynamic(x => "EF.Functions.DateDiffMonth(x.CreatedDate, DateTime.Now) == 0").ToList();
	}
}
catch (Exception ex)
{
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/si58WH' %}

## DisableAutoReplaceDictionaryKey

The DisableAutoReplaceDictionaryKey option lets you get or sets if the feature that allows using the dictionary keys directly in the expression should be disabled. Only keys that are not valid variable names, such as `!@#` or `123`, are replaced automatically in the text which might cause some impact without disabling that option. This option does not impact valid variable names as we never replaced them in the text since they are valid. By default, the DisableAutoReplaceDictionaryKey value is `false`.

In this example, we will first evaluate an expression with an invalid variable name as a key and get the expected result. Then we will evaluate a simple `1+2` expression and get a weird result return as the `2` has been automatically replaced by `12` since this is an invalid variable name. Finally, we will disable the option and now see that `1+2` is giving the expected result again.

```csharp
// Global Context: EvalManager.DefaultContext.DisableAutoReplaceDictionaryKey = true;

var context = new EvalContext();
context.UseCache = false;
var dict = new Dictionary<string, object>();
dict.Add("!@#", 12);
dict.Add("2", 12);

// Automatically replacing the dictionary keys that are not a valid variable names can be something useful
var r1 = context.Execute<int>("1 + !@#", dict); // return 13 (1 + 12)

// But it can also sometimes have some weird side impact
var r2 = context.Execute<int>("1 + 2", dict); // return 13 (1 + 12)

// This option allows avoiding those side impact
context.DisableAutoReplaceDictionaryKey = true;
var r3 = context.Execute<int>("1 + 2", dict); // return 3
```

{% include component-try-it.html href='https://dotnetfiddle.net/G8UeDH' %}

## DisableConstantFolding

The DisableConstantFolding option lets you to get or set if constant folding should be disabled. Constant folding is the logic to replace an equation with 2 constants like `2+3` directly into the constant `5`. This avoids running the addition every time the compiled method is executed.

In this example, we will compile a basic addition expression with 2 constants. By exploring the last compiled expression, we will show the difference when a constant was used with constant folding versus when the addition was used with constant folding turned off.

```csharp
// Global Context: EvalManager.DefaultContext.DisableConstantFolding = true;

var expression = "2+3";

// Example 1 - with constant folding enabled
{
	var context = new EvalContext();	
	var compiled = context.Compile<Func<int>>(expression);
	var lastExpression = context.LastCompiledExpression;
	
	Console.WriteLine($"Constant Folding Enabled; NodeType: {lastExpression.NodeType}; Value {((ConstantExpression)lastExpression).Value}");
}	

// Example 1 - with constant folding disabled
{
	var context = new EvalContext();
	context.DisableConstantFolding = true;
	var compiled = context.Compile<Func<int>>(expression);
	var lastExpression = context.LastCompiledExpression;
	
	Console.WriteLine($"Constant Folding Disabled; NodeType: {lastExpression.NodeType}; LeftValue: {((ConstantExpression)((BinaryExpression)lastExpression).Left).Value}; RightValue: {((ConstantExpression)((BinaryExpression)lastExpression).Right).Value};");
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/u205UR' %}

## DisableDynamicResolution

The DisableDynamicResolution option lets you get or sets if built-in logic should be disabled when solving expression of type `object`. That built-in logic allows you to compile some C# code that would be invalid. By default, the DisableDynamicResolution value is `false`.

In this example, we will first add two `int` values by casting them as type `object`, which is an invalid C# statement but works in this case due to our built-in logic. Then we will try again by disabling that option which will now correctly throw an error.

```csharp
// Global Context: EvalManager.DefaultContext.DisableDynamicResolution = true;

var context = new EvalContext();	

var r1 = context.Execute<int>("(object)1 + (object)2");

try
{
	context.DisableDynamicResolution = true;
	var fail = context.Execute<int>("(object)1 + (object)2");
}
catch (Exception ex)
{
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/00IN7J' %}


## DynamicGetMemberMissingValueFactory

The DynamicGetMemberMissingValueFactory option lets you get or sets a factory to return a value when a member is not found from an entity or ExpandoObject. In other words, it allows specifying a value for a missing member on demand. By default, the DynamicGetMemberMissingValueFactory value is `null`.

In this example, we will first set our DynamicGetMemberMissingValueFactory option, then use it with an entity customer, and after use it again with an ExpandoObject. In both cases, we will try to retrieve the member `FullName`, which doesn't exist but still return a value correctly due to the missing member factory previously set.

```csharp
// Global Context: EvalManager.DefaultContext.DynamicGetMemberMissingValueFactory = (obj, propertyOrFieldName) => { };

var context = new EvalContext();

context.DynamicGetMemberMissingValueFactory = (obj, propertyOrFieldName) =>
{
	if(obj is Customer customer && propertyOrFieldName == "FullName")			
	{		
		// resolution for `Customer`
		return $"{customer.FirstName} {customer.LastName}";
	}
	else
	{
		// resolution for `ExpandoObject`
		dynamic expando = obj;
		return $"{expando.FirstName} {expando.LastName}";
	}
	
	throw new Exception("Not found");
};

// Entity
{
	var customer = new Customer();
	customer.FirstName = "C# Eval";
	customer.LastName = "Expression";

	var r1 = context.Execute("customer.FullName", new { customer });
	Console.WriteLine("1 - Result: " + r1);
}


// ExpandoObject
{
	dynamic customer = new ExpandoObject();
	customer.FirstName = "ZZZ";
	customer.LastName = "Projects";

	var r1 = context.Execute("customer.FullName", new { customer });
	Console.WriteLine("1 - Result: " + r1);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/7d4YuH' %}


## DynamicMemberNames

The DynamicMemberNames option lets you when compiling or executing an expression using an ExpandoObject as a parameter, retrieve all member names that we assume will be a member of the ExpandoObject. For example, suppose the `CustomerID` is returning from the member names list, in that case, the library expects the ExpandoObject will have a property and a value for this `CustomerID` to be executed correctly. By default, the DynamicMemberNames value is `null`.

In this example, we will first compile an expression and then, by looking at the `context.DynamicMemberNames` see which member names the library expects the `ExpandoObject` contains. In the second example, we will try to execute an expression that fails and check in the catch section which member names were expecting to be provided by the `ExpandoObject`.

```csharp
// Global Context: EvalManager.DefaultContext.DynamicMemberNames

var context = new EvalContext();

// required to allow to get member from the compile multiple from `Customer` and `ExpandoObject`
context.IncludeMemberFromAllParameters = true;

// compile example
{
	var c1 = context.Compile<Func<Customer, ExpandoObject, bool>>("FirstName == 'C# Eval' && FullName == 'Test' && CreatedDate > #1981-04-13#");	
	FiddleHelper.WriteTable("1 - Expected ExpandoObject members:", context.DynamicMemberNames);
}

// execute example
{
	try
	{
		var customer = new Customer();
		customer.FirstName = "C# Eval";
		customer.LastName = "Expression";

		dynamic expando = new ExpandoObject();
		expando.FullName = "ZZZ Projects";

		var fail = context.Execute<bool>("FirstName == 'C# Eval' && FullName == 'Test' && CreatedDate > #1981-04-13#", customer, expando);
	}
	catch(Exception ex)
	{		
		Console.WriteLine("2 - Exception: " + ex.Message);
		FiddleHelper.WriteTable("3 - Expected ExpandoObject members:", context.DynamicMemberNames);
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/tUEQ69' %}

## ForceCharAsString

The ForceCharAsString option lets you get or set if a char such as `'z'` must be forced as a string as if you would have specified `"z"`. The library is smart enough to know that the expression `'zzz'` is a string (more than one char) but sometimes cannot know if `'z'` is a string or a char, in particular when a method exists with an overload for a `char` and a `string`. By default, the ForceCharAsString value is`false`.

In this example, we will use a `MethodOverload` method with an overload for both `char` and `string` types. In this first execute resolution, we will see that the method resolution uses the right method by using the overload with the `char` type. In the second resolution, we will force the library by using our option to consider all `char` as a `string` instead and observe that the library now uses the method overload with the `string` type.

```csharp
// Global Context: EvalManager.DefaultContext.ForceCharAsString = true;

var context = new EvalContext();
context.UseCache = false;
context.RegisterStaticMethod(typeof(Program));

var r1 = context.Execute("MethodOverload('z')"); // return "Method with char z"
Console.WriteLine(r1);

context.ForceCharAsString = true;
var r2 = context.Execute("MethodOverload('z')"); // return "Method with string z"
Console.WriteLine(r2);

public static string MethodOverload(char s)
{
	return "Method with char " + s;
}

public static string MethodOverload(string s)
{
	return "Method with string " + s;
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/2dVPsr' %}

## ForceIncludeInstanceMethodFromParameter

The ForceIncludeInstanceMethodFromParameter  option lets you get or set if instance method (found from parameter) should always be included. They are usually included only if no matching method are found.  

In this example, we will register a static method and try to call a method with the overload that only exists in our instance entity. Since at least 1 method has been found, we will see that by default, the instance will not be taken unless we specify `ForceIncludeInstanceMethodFromParameter = true`.

```csharp
// Global Context: EvalManager.DefaultContext.ForceIncludeInstanceMethodFromParameter = true;

var context = new EvalContext();
context.UseCache = false;
context.RegisterStaticMethod(typeof(Program.MyStaticMethod));

try
{
	var r1 = context.Execute("MyMethod(1, 2)", new MyEntity());
}
catch(Exception ex)
{
	Console.WriteLine("1 - Exception: " + ex.Message);
}

context.ForceIncludeInstanceMethodFromParameter = true;
var r2 = context.Execute("MyMethod(1, 2)", new MyEntity());
Console.WriteLine("2 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/ttTURM' %}

## ForceObjectAsDynamic

The ForceObjectAsDynamic option lets you get or set if the type `object` should for operator `==` (equals) and `!=` (not equals) compare by reference or value. By default, the ForceObjectAsDynamic value is `false` (compared by reference).

In this example, we will first compare 2 objects with an `int` value by directly using C# statement to show the difference between an equals operator `==` and the `Equals` method. Then we will use our library first directly with the equals operator, then again, but this time, with the `ForceObjectAsDynamic = true` option.

```csharp
// Global Context: EvalManager.DefaultContext.ForceObjectAsDynamic = true;

var context = new EvalContext();
context.UseCache = false;

object x = 1;
object y = 1;

Console.WriteLine("1 - Result: " + (x == y).ToString()); // false
Console.WriteLine("2 - Result: " + x.Equals(y)); // true

Console.WriteLine("3 - Result: " + context.Execute("x == y", new { x, y })); // false
context.ForceObjectAsDynamic = true;
Console.WriteLine("4 - Result: " + context.Execute("x == y", new { x, y })); // true
```

{% include component-try-it.html href='https://dotnetfiddle.net/jVAl0W' %}

> NOTE: The option might be eventually renamed to reflect the purpose better. It was initially created to handle more scenarios we now solve by default.

## IncludeMemberFromAllParameters

The IncludeMemberFromAllParameters option lets you get or set if we should include all members from all parameters, like the default logic, automatically does when only one parameter is specified. So, for example, if you specify a customer and an invoice, you can use the `CustomerID` and `InvoiceID` properties directly. If a member name exists in more than one parameter, such as `ID`, the first member will be used, and all others be ignored. By default, the IncludeMemberFromAllParameter value is `false`.

In this example, we will first try to resolve our expression with the default behavior, which will fail as you cannot use the member name directly when multiple parameters are specified. Then, we will try again, but this time, with the `IncludeMemberFromAllParameters = true` option, which will make our expression execute successfully as we can use member names of all parameters in the expression.

```csharp
// Global Context: EvalManager.DefaultContext.IncludeMemberFromAllParameters = true;

var context = new EvalContext();
context.UseCache = false;

var customer = new Customer() { Name = "C# Eval Expression" };
var invoice = new Invoice() { TotalQuantity = 13 };

try
{
	// 1 - the library include default member name when only 1 parameter is specified but not for multiples parameters
	var fail = context.Execute("return $'the customer {customer.Name} ordered {invoice.TotalQuantity} items';", new { customer }, new { invoice});
}
catch(Exception ex)
{		
	Console.WriteLine("1 - Exception: " + ex.Message);
}

// 2 - however, you can have the behavior to include members of all parameters with the option `IncludeMemberFromAllParameters`
context.IncludeMemberFromAllParameters = true;
var r2 = context.Execute("return $'the customer {customer.Name} ordered {invoice.TotalQuantity} items';", new { customer }, new { invoice});
Console.WriteLine("2 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/s0x5sW' %}

## IsCaseSensitive

The IsCaseSensitive option lets you get or set the flags when searching for a member (Property, Field, Method) if the search should be case sensitive. Under the hood, the `IgnoreCase` binding flag is added or removed to the `BindingFlags` options. By default, the IsCaseSensitive value is true or the value assigned to the `BindingFlags` options if already modified.

In this example, we will first evaluate an expression by accessing a property with the wrong casing, which will succeed as the library is case insensitive by default. Then, we will set the `IsCaseSensitive = true;` to force the library to be case sensitive and then try again to access the property value, which will raise an error.

```csharp
// Global Context: EvalManager.IsCaseSensitive = true;

var context = new EvalContext();
var entity = new Entity() { EntityID = 13 };

var entityID = context.Execute("entity.enTItyiD", new { entity });
Console.WriteLine("1 - EntityID: " + entityID);

try
{
	context.IsCaseSensitive = true;
	
	var fail = context.Execute("entity.enTItyiD", new { entity });
}
catch(Exception ex)
{
	Console.WriteLine("2 - Exception: " + ex.Message);
}
```

## LastCompiledExpression

The LastCompiledExpression option lets you to get the expression that was used in the last compilation. This option is not thread safe, so it should always be used in a new instance of an eval context if your application is multi-threaded.

In this example, we will compile a basic C# statement and explore the expression that was compiled.

```csharp
// Global Context: var lastCompiledExpression = EvalManager.DefaultContext.LastCompiledExpression;

var context = new EvalContext();

var compiled = context.Compile<Func<int, int, int>>("X + Y", "X", "Y");
var lastExpression = context.LastCompiledExpression;

Console.WriteLine($"Root NodeType: {lastExpression.NodeType};");
Console.WriteLine($"Left NodeType: {((BinaryExpression)lastExpression).Left.NodeType}");
Console.WriteLine($"Right NodeType: {((BinaryExpression)lastExpression).Right.NodeType}");
```

{% include component-try-it.html href='https://dotnetfiddle.net/RE6pa0' %}

## LastCompiledParameterExpressions

The LastCompiledParameterExpressions option lets you to get the list of parameter expression that was used in the last compilation. This option is not thread safe, so it should always be used in a new instance of an eval context if your application is multi-threaded.

In this example, we will compile a basic C# statement with 2 parameters and explore the 2 parameters expression that was used for the compilation.

```csharp
// Global Context: var lastCompiledParameterExpressions = EvalManager.DefaultContext.LastCompiledParameterExpressions;

var context = new EvalContext();

var compiled = context.Compile<Func<int, int, int>>("X + Y", "X", "Y");
var lastParameterExpressions = context.LastCompiledParameterExpressions;

foreach(var parameterExpression in lastParameterExpressions)
{
	Console.WriteLine($"ParameterName: {parameterExpression.Name}; ParameterType: {parameterExpression.Type}");
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/8ay2L2' %}

## MaxLoopIteration

The MaxLoopIteration option lets you get or set the maximum of iteration allowed for a loop (do, while, for, foreach). For example, if you execute an expression from a user input but want to avoid an infinite loop, you can set the option `MaxLoopIteration = 25` to limit it to 25 iterations. By default, the MaxLoopIteration value is `null` (unlimited).

In this example, we will first execute a code in a `while` loop that will iterate 100 times. Then we will set the `MaxLoopIteration = 25` and execute the same code again, but this time will throw as it will reaches the maximum loop allowed in an expression.

```csharp
// Global Context: EvalManager.MaxLoopIteration = 25;

var context = new EvalContext();

// without MaxLoopIteration
{
	var r1 = context.Execute<int>(@"
var i = 0;

while (i < 100)
{
	i++
}

return i;");
		
	Console.WriteLine("1 - Result: " + r1);
}

		
// with MaxLoopIteration
{
	context.MaxLoopIteration = 25;

	try
	{
		var fail = context.Execute<int>(@"
var i = 0;

while (i < 100)
{
	i++
}

return i;");

	}
	catch(Exception ex)
	{
		Console.WriteLine("2 - Exception: " + ex.Message);
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/dtS98k' %}

## MemoryCacheEntryOptionsFactory

The MemoryCacheEntryOptionsFactory option lets you get or set for .NET 5+ and .NET Core the cache entry options to use when caching the delegate returned from the compile method. By default, the MemoryCacheEntryOptionsFactory value is `new MemoryCacheEntryOptions() { Size = 1 };`.

In this example, we will set a new MemoryCacheEntryOptionsFactory that will set a `SlidingExpiration` to only cache a compile delegate 5 minutes.

```csharp
// Global Context: EvalManager.DefaultContext.MemoryCacheEntryOptionsFactory = () => new MemoryCacheEntryOptions() { Size = 1, SlidingExpiration = TimeSpan.FromMinutes(5) };

var context = new EvalContext();
context.MemoryCacheEntryOptionsFactory = () => new MemoryCacheEntryOptions() { Size = 1, SlidingExpiration = TimeSpan.FromMinutes(5) };

Console.WriteLine("1 - Result: " + context.Execute("1 + 2"));
```

{% include component-try-it.html href='https://dotnetfiddle.net/pJEYDB' %}

## MissingTypes

The MissingType option lets you get or set the missing types to make an expression valid. The list is automatically populated when the option `AutoAddMissingTypes` or `RetryAndThrowMissingTypes` is used. By default, the MissingType value is `null`.

In this example, we will try to evaluate an expression the first time without the `AutoAddMissingTypes` option, which will fail. Then we will evaluate the expression with the option `AutoAddMissingTypes = true`, which will succeed, and then we will show which missed types we automatically added. Finally, we will try again with the option `RetryAndThrowMissingTypes = true;` and show which types are missing to make the expression work.

```csharp
// Global Context: EvalManager.DefaultContext.MissingTypes;

try
{
	var context = new EvalContext();
	var fail = (Entity)context.Execute("return new Entity() { EntityID = 13 }");
}
catch(Exception ex)
{
	Console.WriteLine("1 - Exception: " + ex.Message);
}

// Using the `AutoAddMissingTypes`
{
	var context = new EvalContext();
	context.AutoAddMissingTypes = true;
	var entity = (Entity)context.Execute("return new Entity() { EntityID = 13 }");

	Console.WriteLine("2 - EntityID: " + entity.EntityID);
	
	foreach(var missingType in context.MissingTypes)
	{
		Console.WriteLine("3 - Missing Types: " + missingType.FullName);
	}
}

// Using the `RetryAndThrowMissingTypes`
{
	var context = new EvalContext();
	context.RetryAndThrowMissingTypes = true;
	
	try
	{
		var entity = (Entity)context.Execute("return new Entity() { EntityID = 13 }");
	}
	catch(Exception ex)
	{
		Console.WriteLine("4 - Exception: " + ex.Message);
		
		foreach(var missingType in context.MissingTypes)
		{
			Console.WriteLine("5 - Missing Types: " + missingType.FullName);
		}
	}			
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/f8xSIN' %}

## RetryAndThrowMissingTypes

The RetryAndThrowMissingTypes option lets you get or set if an error should be thrown by trying again to compile the expression and populate the `MissingTypes` list with types missing to make an expression valid. To compile an expression by automatically adding missing types, use the `AutoAddMissingTypes` option instead. By default, the RetryAndThrowMissingTypes is `false`.

In this example, we will set the `RetryAndThrowMissingTypes = true;` option and try to evaluate an expression. First, we will see that an exception is thrown with the message containing all missing types, and then we will show them again through the `MissingTypes` option.

```csharp
// Global Context: EvalManager.DefaultContext.RetryAndThrowMissingTypes = true;

var context = new EvalContext();
context.RetryAndThrowMissingTypes = true;

try
{
	var entity = (Entity)context.Execute("return new Entity() { EntityID = 13 }");
}
catch(Exception ex)
{
	Console.WriteLine("4 - Exception: " + ex.Message);

	foreach(var missingType in context.MissingTypes)
	{
		Console.WriteLine("5 - Missing Types: " + missingType.FullName);
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/gL1l7H' %}

## SafeMode

The SafeMode option lets you get or set if only members and types registered can be used for the expression. In other words, you can safely execute user input in your environment by restricting him to use only what you allow. Make sure to read the [SafeMode](/safe-mode) documentation if you plan to use this option. By default, the SafeMode value is `false`.

```csharp
// Global Context: EvalManager.DefaultContext.SafeMode = true;

var context = new EvalContext();

context.SafeMode = true;
context.UnregisterAll();

try
{
	var fail = context.Execute("Math.Min(1, 2)");
	Console.WriteLine(fail);
}
catch(Exception ex)
{
	Console.WriteLine("1 - Exception: " + ex.Message);
}

// try again by registering "System.Math" member
context.RegisterMember(typeof(System.Math));
var r2 = context.Execute("Math.Min(1, 2)");
Console.WriteLine("2 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/ljr0y2' %}

## UseCache

The UseCache option lets you get or set if the cache should be used to retrieve or cache the delegate created from the compile method. By default, the UseCache value is `true`.

In this example, we will evaluate a simple expression and then again by modifying the `DisableAutoReplaceDictionaryKey = true` option. The second `Execute` will return the same result as the first one since the delegate has been taken from the cache. Then we will set our option `UseCache = false` and get this time the correct result we are expecting.

```csharp
// Global Context: EvalManager.DefaultContext.UseCache = false;

var context = new EvalContext();

var dict = new Dictionary<string, object>();
dict.Add("2", 12);

var r1 = context.Execute<int>("1 + 2", dict); // return 13 (1 + 12)
Console.WriteLine("1 - Result: " + r1); 

context.DisableAutoReplaceDictionaryKey = true;

var r2 = context.Execute<int>("1 + 2", dict); // return 13 (1 + 12) because of caching
Console.WriteLine("2 - Result: " + r2);

context.UseCache = false;
var r3 = context.Execute<int>("1 + 2", dict); // return 3
Console.WriteLine("2 - Result: " + r3);
```

{% include component-try-it.html href='https://dotnetfiddle.net/ILSwZx' %}

## UseCaretForExponent

The UseCaretForExponent option lets you get or set if the caret character `^` should behave as an exponent operator instead of an XOR (logical exclusive OR) operator. For example, you want the following expression, `2^3` to return 8 (2 to the power of 3). The double caret `^^` (`2^^3`) already acts as an exponent operator whether this option is enabled or not. By default, the UseCaretForExponent value is `false`.

In this example, we will use the math expression 2 power 3 by first using a double caret and then by setting our `UseCaretForExponent = true` option and using a single caret. In both cases, the result will be 8.

```csharp
// Global Context: EvalManager.DefaultContext.UseCaretForExponent = true;

var context = new EvalContext();

// already work without the UseCaretForExponent option	
var r1 = context.Execute<int>("2 ^^ 3"); // return 8
Console.WriteLine("1 - Result: " + r1);

context.UseCaretForExponent = true;
var r2 = context.Execute<int>("2 ^ 3"); // return 8
Console.WriteLine("2 - Result: " + r2); 
```

{% include component-try-it.html href='https://dotnetfiddle.net/RAFhDq' %}

> NOTE: A new operator '^|' is available for the bitwise exclusive-OR operator.

## UseEqualsAssignmentAsEqualsOperator

The UseEqualsAssignmentAsEqualsOperator option lets you get or set if the equal assignation `=` should instead act like an equal operator. For example, you evaluate some math formula and would want the expression `2+2=4` instead of trying to make a variable assignment. By default, the UseEqualsAssignmentAsEqualsOperator value is `false`.

In this example, we will use the expression `x = 3` in both cases. In the first `Execute`, the equal operator will be used as an assignment which is the default behavior. In the second `Execute`, we will set the `UseEqualsAssignmentAsEqualsOperator = true;` option and see that the `=` operator behaves like the `==`.

```csharp
// Global Context: EvalManager.DefaultContext.UseEqualsAssignmentAsEqualsOperator = true;

var context = new EvalContext();
context.UseCache = false;

var dict = new Dictionary<string, object>();
dict["x"] = 1;

var r1 = context.Execute<int>("x = 3", dict); // return 3
Console.WriteLine("1 - Result: " + r1);

dict["x"] = 1;
context.UseEqualsAssignmentAsEqualsOperator = true;
var r2 = context.Execute<bool>("x = 3", dict); // return false
Console.WriteLine("2 - Result: " + r2); 
```

{% include component-try-it.html href='https://dotnetfiddle.net/t65NP4' %}


## UseLocalCache

The UseLocalCache option lets you get or set if the global cache (for all eval context) or a local cache (for this eval context) should be used when we retrieve or cache the delegate created from the compile method. By default, the UseLocalCache value is `false`.

In this example, we will evaluate a simple expression from the global context and then again by using the `DisableAutoReplaceDictionaryKey = true` option. You can comment or uncomment the line `context.UseLocalCache = true;` to see a different result will be returned depending if we take the previously created delegate from the global cache or not.

```csharp
// Global Context: EvalManager.DefaultContext.UseLocalCache = true;

var context = new EvalContext();

var dict = new Dictionary<string, object>();
dict.Add("2", 12);

var r1 = Eval.Execute<int>("1 + 2", dict); // return 13 (1 + 12)
Console.WriteLine("1 - Result: " + r1); 

context.DisableAutoReplaceDictionaryKey = true;

// comment or uncomment to see the different behavior
context.UseLocalCache = true;

var r2 = context.Execute<int>("1 + 2", dict); // return 3 if `UseLocalCache = true`; 13 if `UseLocalCache = false`; 
Console.WriteLine("2 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/lMIIqT' %}


## UseShortCacheKey

The UseShortCacheKey option lets you get or set if the cache key should be shortened. Enabling that option can increase performance, but ensure to read the full `UseShortCacheKey ` documentation before using it. By default, the UseShortCacheKey value is `false`.

In this example, we will set the `UseShortCacheKey = true` and execute dynamically a very simple arithmetic expression.

```csharp
// Global Context: EvalManager.UseShortCacheKey = true;

var context = new EvalContext();
context.UseShortCacheKey = true;

Console.WriteLine("1 - Result: " + context.Execute("1 + 2"));
```

{% include component-try-it.html href='https://dotnetfiddle.net/VEzGOj' %}


## UseSmartExecuteParameterResolution

The UseSmartExecuteParameterResolution option lets you get or set if the smart resolution should be used when passing multiple values to the execute method, like the default logic, which automatically does when only one parameter is specified. This option is very similar to the option `IncludeMemberFromAllParameter` but is different in the way that we also register value to know the type already. By default, the UseSmartExecuteParameterResolution value is `false`.

In this example, we will first try to resolve our expression with the default behavior, which will fail as you cannot use the member name directly when multiple parameters are specified. Then, we will try again, but this time, with the `UseSmartExecuteParameterResolution= true` option, which will make our expression execute successfully as we can use member names of all parameters in the expression.

```csharp
// Global Context: EvalManager.DefaultContext.UseSmartExecuteParameterResolution = true;

var context = new EvalContext();
context.UseCache = false;

var customer = new Customer() { Name = "C# Eval Expression" };
var invoice = new Invoice() { TotalQuantity = 13 };

try
{
	// 1 - the library include default member name when only 1 parameter is specified but not for multiples parameters
	var fail = context.Execute("return $'the customer {customer.Name} ordered {invoice.TotalQuantity} items';", new { customer }, new { invoice});
}
catch(Exception ex)
{		
	Console.WriteLine("1 - Exception: " + ex.Message);
}

// 2 - however, you can have the behavior to include members of all parameters with the option `UseSmartExecuteParameterResolution`
context.UseSmartExecuteParameterResolution = true;
var r2 = context.Execute("return $'the customer {customer.Name} ordered {invoice.TotalQuantity} items';", new { customer }, new { invoice});
Console.WriteLine("2 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/nQPsCp' %}

## UseSmartTypeResolution

The UseSmartTypeResolution option lets you get or set if some type should be found automatically without specifying the `System` namespace. For example, when enabled, you can automatically use `Math.Min`, but when disabled, you need to use`System.Math.Min`. By default, the UseSmartTypeResolution value is `true`.

In this example, we will first create a new instance of `System.Diagnostics.Stopwatch()` but only specify the `Diagnostics.Stopwatch()` part in our expression. As we will see, the `Stopwatch` type was found as the library automatically added the `System` part as no type was initially found. In the second example, we will turn off this option and see now that the library raises an exception since the type was not found.

```csharp
// Global Context: EvalManager.DefaultContext.UseSmartTypeResolution = false;

var context = new EvalContext();
context.UseCache = false;

var r1 = context.Execute("new Diagnostics.Stopwatch();");
Console.WriteLine("1 - Result: " + r1);

try
{
	context.UseSmartTypeResolution = false;

	var fail = context.Execute("new Diagnostics.Stopwatch();");
}
catch(Exception ex)
{		
	Console.WriteLine("1 - Exception: " + ex.Message);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/cTt5lB' %}

## UseTypeBeforeDynamic

The UseTypeBeforeDynamic option lets you get or set if, for some expression, the logic using the type should have priority over the resolution using dynamic. By default, the UseTypeBeforeDynamicvalue is `false`.

In this example, we will register the `Type` program, which contains the member `ValueForTypeOrDynamic` and we will create an `ExpandoObject` which contains a member `Program.ValueForTypeOrDynamic`. In the first expression, the value returned will be the value from the expando, which is the default expected behavior. In the second expression, we will set our `UseTypeBeforeDynamic = true;` option, and this time, the value returned will be the one from our type as we tell our library to use the type before the expando when one is found.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.UseTypeBeforeDynamic = true;
		
		var context = new EvalContext();
		context.UseCache = false;
		context.RegisterStaticMember(typeof(Program));
		
		dynamic expando = new ExpandoObject();
		expando.Program = new ExpandoObject();
		expando.Program.ValueForTypeOrDynamic = 99;
		
		var r1 = context.Execute("Program.ValueForTypeOrDynamic", expando); // return 99
		Console.WriteLine("1 - Result: " + r1);
		
		context.UseTypeBeforeDynamic = true;
		var r2 = context.Execute("ValueForTypeOrDynamic", expando); // return 13
		Console.WriteLine("2 - Result: " + r2);
	}
	
	public static int ValueForTypeOrDynamic = 13;
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/tzlM6B' %}

## VariableFactory

The VariableFactory option lets you get or sets a factory to return a value when a variable is not found. In other words, it allows specifying a value for a variable on demand. By default, the VariableFactory value is `null`.

In this example, we will first execute a simple expression `X + 2` without specifying any value for `X`, which will fail. Then we will set the `VariableFactory` option to specify how to handle missing variables. In our case, we will set `1` as the value for `X` and specify that the missing variable name has been handled in this case. Finally, we will try our simple `X+2` expression again, and this time, it will return `3` successfully.

```csharp
// Global Context: EvalManager.DefaultContext.VariableFactory = arg => { }

var context = new EvalContext();
context.UseCache = false;

try
{
	var fail = context.Execute("X + 2");
}
catch(Exception ex)
{		
	Console.WriteLine("1 - Exception: " + ex.Message);
}

context.VariableFactory = arg =>
	{
		var name = arg.Name;
		if (name == "X")
		{
			arg.Value = 1;
			arg.IsHandled = true;
		}
	};

var r2 = context.Execute("X + 2");
Console.WriteLine("1 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/WA3wIH' %}