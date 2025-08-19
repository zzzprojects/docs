---
Name: Safe Mode Option
LastMod: 2025-08-19
---

# Safe Mode Option

### Problem
You want to allow your users to create and evaluate some generic expression. However, you need to limit access on what the user can do!

### Solution
Use the **SafeMode** property.

When enabled, the user can only use registered:
- Constructor
- Field
- Method
- Property

##### Example
```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var context = new EvalContext();
context.SafeMode = true;
context.UnregisterAll();
context.RegisterDefaultAliasSafe();

return context.Eval("1+2");
```
