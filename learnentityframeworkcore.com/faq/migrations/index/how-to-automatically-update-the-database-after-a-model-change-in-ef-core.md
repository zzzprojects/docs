---
id: a68b4570-1bca-415b-8c04-4d4d9962ba5a
position: 3
title: How to automatically update the database after a model changes in EF Core?
---

EF Core doesn't automatically update the database. A migration script must manually be [created](https://www.learnentityframeworkcore.com/migrations#creating-a-migration)

Once created, you can automatically apply the migration script when the application starts with the `Migrate` method.

```csharp
context.Database.Migrate();
```

Or manually [update your database](https://www.learnentityframeworkcore.com/migrations#ef-core-update-database)