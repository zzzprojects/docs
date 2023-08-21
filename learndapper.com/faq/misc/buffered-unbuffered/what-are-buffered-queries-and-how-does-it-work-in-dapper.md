---
id: 4898b307-7fd4-40cd-b0ec-a5d277641723
position: 1
title: What are buffered queries and how does it work in Dapper?
---

Buffered queries in Dapper refer to how Dapper handles the results of a query. When a query is executed in buffered mode, Dapper loads the entire result set into memory at once. That means all the data from the query is immediately available for processing, but it can consume more memory for large result sets. Buffered queries are the default behavior in Dapper.
