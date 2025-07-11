---
id: 1e928117-8280-4a86-9bfb-177c30953d62
position: 4
title: How to reset all migrations and create a new clean database in EF Core?
---

With Package Manager Console:

1. **Delete your database**: `drop-database`
2. **Delete your migrations**: Delete all migrations manually in your project.
3. **Update connection string**: Change your connection string if needed (e.g., if you use a new database name)
4. **Add the first migration**: `add-migration [name]`

With Command Line:

1. **dotnet ef database drop**: `drop-database`
2. **Delete your migrations**: Delete all migrations manually in your project.
3. **Update connection string**: Change your connection string if needed (e.g., if you use a new database name)
4. **Add the first migration**: `dotnet ef migrations add [name]`