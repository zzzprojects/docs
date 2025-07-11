---
id: f7145e5b-9b78-4b12-bb7b-28b75bc29488
position: 3
title: When a DbUpdateConcurrencyException occurs in EF Core?
---

In EF Core, a `DbUpdateConcurrencyException` occurs when an attempt to save changes in the database is in conflict with a change made by another user or process before.

The `DbUpdateConcurrencyException` happens when the data is updated or deleted and has already been modified by another transaction after it was originally fetched, resulting in a concurrency conflict.