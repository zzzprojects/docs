---
id: bdc6b9bd-15d4-434b-bfdc-47a9ed845895
position: 2
title: What does the buffered parameter for the Query and QueryAsync methods in Dapper?
---

The [buffered parameter](https://www.learndapper.com/misc/buffered-unbuffered) in Dapper for the `Query` and `QueryAsync` methods controls whether the results of a query are loaded into memory all at once (buffered) or streamed one by one (unbuffered). If set to `true` (the default), all the data from the query is loaded into memory immediately. If set to `false`, each row is loaded and processed one at a time, saving memory but potentially increasing execution time.

```csharp
// Query result will be streamed one by one when `buffered = false`
var customers = connection.Query<Customer>(sql, buffered: false);
```