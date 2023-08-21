---
id: fc53ef6a-1d67-4cfa-8fb8-62b812ac444b
position: 5
title: What are unbuffered queries and how does it work in Dapper?
---

Unbuffered queries in Dapper refer to a method of handling query results where the data is not loaded into memory all at once. Instead, each row is loaded and processed one at a time, like a stream. This method is more memory-efficient for large data sets, but the data may not be immediately available for processing like in a buffered query. You enable unbuffered queries by setting the `buffered` parameter to `false` when executing a query in Dapper.
