---
Name: Eval Manager
LastMod: 2025-08-19
---

# Eval Manager

## Description
Static manager class for options shared between all instances.

## Cache
Gets or sets the cache to use to cache compiled delegate.

### Example
```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

EvalManager.Cache = MemoryCache.Default;
```

> You can use your own cache provider inheriting from System.Runtime.Caching.ObjectCache

## DefaultContext
Gets or sets the default context to use for all operations using the default context.

You can also directly change options
 - [Register & Unregister](register-unregister)
 - [Options](options)

The default context is used in static methods:
- [Eval.Execute](eval-execute)
- [Eval.Compile](eval-compile)
- [LINQ Dynamic](linq-dynamic)
- [string.Execute](string-extensions#stringexecute)
- [string.Compile](string-extensions#stringcompile)

### Example
```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

EvalManager.DefaultContext.RegisterExtensionMethod(typeof(Z.ExtensionMethods))

// Make member case insensitive (Math.pOW = Math.Pow)
EvalManager.DefaultContext.BindingFlags = BindingFlags.IgnoreCase | context.BindingFlags
```


