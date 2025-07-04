---
Permalink: ef-core-audit-property-unchanged
Name: Audit - Property Unchanged
---

# Audit - Property Unchanged

## Problem

You need to keep track of all changed and unchanged properties

## Solution

You can choose whether or not to ignore unchanged properties with `IgnorePropertyUnchanged`.

By default, properties unchanged are ignored unless they're part of the primary key.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// Globally
AuditManager.DefaultConfiguration.IgnorePropertyUnchanged = false;

// By Instance
var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
audit.Configuration.IgnorePropertyUnchanged = false;
ctx.SaveChanges(audit);

```
[Try it](https://dotnetfiddle.net/6oWfr2)
