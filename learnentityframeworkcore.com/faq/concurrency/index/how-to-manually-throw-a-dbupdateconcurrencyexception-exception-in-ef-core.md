---
id: c7de8efc-5421-478c-b656-8e4ba4902595
position: 5
title: How to manually throw a DbUpdateConcurrencyException exception in EF Core?
---

You first need to create a new instance of the `Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException` class by specifying your error message.

```csharp
throw new Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException("[Error Message]", new List<IUpdateEntry> { null }  );
```

See all [DbUpdateConcurrencyException Constructors](https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore/DbUpdateConcurrencyException.cs)