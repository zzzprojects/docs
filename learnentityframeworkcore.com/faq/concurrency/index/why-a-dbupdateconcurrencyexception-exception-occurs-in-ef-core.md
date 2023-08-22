---
id: 8fafbb3c-0d73-4b9c-b6ef-5a5b3cabb459
position: 2
title: Why a DbUpdateConcurrencyException exception occurs in EF Core?
---

In EF Core, a `DbUpdateConcurrencyException` occurs to ensure that multiple users don't overwrite each other's data due to the optimistic concurrency strategy.

If there is a difference between the data you want to update and the current database value, EF Core raises the `DbUpdateConcurrencyException` to signal the concurrency conflict. This mechanism helps maintain data integrity in scenarios with concurrent data access.