---
id: 3d1df34f-fd0b-4527-8ae8-af4d20641d7a
position: 1
title: What is a DbUpdateConcurrencyException exception in EF Core?
---

In EF Core, a `DbUpdateConcurrencyException` is thrown when there's a conflict while trying to save changes to the database.

For example, let's say the current customer name is `Joathan`, but you want to fix the name and update it to `Jonathan`; meanwhile, already someone else has updated it to `Mikael`. A `DbUpdateConcurrencyException` must be manually solved to know if we should keep the new database value or overwrite it.