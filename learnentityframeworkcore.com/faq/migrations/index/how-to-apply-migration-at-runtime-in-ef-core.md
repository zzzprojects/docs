---
id: 2646a77f-d6e7-4186-abdd-5d2c4fdbc40d
position: 1
title: How to apply migration at runtime in EF Core?
---

You can apply any pending migration at runtime with the `context.Database.Migrate()` method:

```csharp
using (var context = new MyContext())
{
    context.Database.Migrate();
}
```