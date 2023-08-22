---
id: 5a4149ea-6f6a-4ccb-a23e-7a5073e98522
position: 4
title: How is determined when to throw a DbUpdateConcurrencyException in EF Core?
---

In EF Core, the decision to throw a `DbUpdateConcurrencyException` is based on the number of affected rows returned. The check for the concurrency token is appended to the `WHERE` clause, and when performing an `UPDATE` or `DELETE`, if no rows were affected, it means there is currently a concurrency conflict.

Here is a more detailed answer:

1. **Concurrency Tokens**: When defining your entity model, you can mark a property as a concurrency token:
   - With the [[ConcurrencyCheck]](/configuration/data-annotation-attributes/concurrencycheck-attribute) data annotation
   - With the [[TimeStamp]](/configuration/data-annotation-attributes/timestamp-attribute) data annoation
   - With the [IsConcurrencyToken()](/configuration/fluent-api/isconcurrencytoken-method) fluent API
   - With the [IsRowVersion()](/configuration/fluent-api/isrowversion-method) fluent API
2. **Database Operations**: When performing an update or delete operation, EF Core includes these concurrency tokens in the SQL `WHERE` clause. 
3. **Check the Affected Rows**: After executing the operation, EF Core examines the number of affected rows. 
   - If no rows were affected, it means that no records in the database matched. This is interpreted as a concurrency conflict.
4. **Throw the Exception**: If such a conflict is detected, EF Core throws a `DbUpdateConcurrencyException`.