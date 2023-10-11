---
id: f96f140a-4e04-4433-8d27-be9e2e26beba
position: 4
title: How to insert data using a list in Dapper?
---

To insert data using a list, you need to pass the list in a parameter instead of a single object to the `Execute` or `ExecuteAsync` method. However, inserting by providing a list doesn't perform a bulk insert. Instead, it simply iterates over every item to perform a single insert. See the [Inserting multiple rows](#dapper-insert-multiple-rows) for an example.

See the [Bulk Inserting](https://www.learndapper.com/bulk-operations/bulk-insert) documentation if you want to insert the list in bulk.