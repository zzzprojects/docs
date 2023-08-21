---
id: b1f79c47-b7f4-46a9-975f-a0051b6dcf21
position: 10
title: How can I set queries to be unbuffered by default in Dapper?
---

Unfortunately, Dapper doesn't have any options to change this behavior. If you want to use an unbuffered query, you must explicitly set the `buffered` parameter to `false` when executing the query.

```csharp
var customers = connection.Query<Customer>(sql, buffered: false);
```