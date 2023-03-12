---
Permalink: breaking-changes
Name: Breaking Changes
---

# Breaking Changes

## v1.3.0
A breaking change is introduced in version 1.3.0 which is related to calling methods on classes.
Due to security reasons, it's now only allowed to call methods on the standard predefined classes like (`bool`, `int`, `string` ...).
If you want to call a method on an own custom class, annotate that class with the [DynamicLinqType](https://dynamic-linq.net/advanced-extending#dynamiclinqtype-attribute).
Example:
``` c#
[DynamicLinqType]
public class MyCustomClass
{
    public int GetAge(int x) => x;
}
```

If it's not possible to add that attribute, you need to implement a custom [CustomTypeProvider](https://dynamic-linq.net/advanced-configuration#customtypeprovider) and set this to the `ParsingConfig` and provide that config to the dynamic call.