---
id: 229e462e-f4da-453c-a12f-69c2d312f494
position: 2
title: How can I upgrade EF6 migrations to EF Core?
---

You cannot upgrade from EF6 migrations to EF Core. It's recommended to apply all EF6 migrations to your database before switching to EF Core. Then you can use EF Core for future migrations.

You can find more information about [Porting from EF6 to EF Core](https://learn.microsoft.com/en-us/ef/efcore-and-ef6/porting/port-code#existing-migrations)