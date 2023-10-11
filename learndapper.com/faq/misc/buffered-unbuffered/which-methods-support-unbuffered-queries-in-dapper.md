---
id: 0e427008-58b7-4f2e-8588-3e044ead738d
position: 8
title: Which methods support unbuffered queries in Dapper?
---

In Dapper, only two base methods support unbuffered queries:

- **`Query` / `QueryAsync`**: You can set the buffered parameter to false to the [Query / QueryAync methods](https://www.learndapper.com/dapper-query/selecting-multiple-rows) to use unbuffered queries.
- **`Read` / `ReadAsync`**: You can use those methods from the `GridReader` returned by [QueryMultiple / QueryMultipleAsync methods](https://www.learndapper.com/dapper-query/selecting-multiple-results).
 