---
id: 65e32f76-40a3-41d3-bdc1-da7aa8a0e6fd
position: 3
title: What are the cons of using buffered queries in Dapper?
---

- **Memory Consumption**: Buffered queries load the entire result set into memory at once, consuming a significant amount of memory for large datasets.
- **Initial Load Time**: There may be a noticeable delay before the first record is available for large result sets, as Dapper loads all the data into memory.
- **Resource Usage**: High memory usage might lead to system slowdowns or out-of-memory errors in resource-constrained environments.
- **Unsuitable for Large Data Sets**: Due to the above reasons, buffered queries may not be the best choice when dealing with very large data sets.
- **Connection Lifetime**: The connection remains open until all data is fetched, which could be problematic in the case of large result sets or slow networks.