---
id: f9404ea9-85dc-4e7c-8c72-30b308e18d4b
position: 6
title: What are the pros of using unbuffered queries in Dapper?
---

- **Memory Efficiency**: Unbuffered queries load data one row at a time, which can be more memory-efficient for large result sets.
- **Better for Large Data Sets**: Because of the streaming nature of unbuffered queries, they're better suited to handling large data sets that would be impractical or impossible to load into memory all at once.
- **Lower Initial Load Time**: Unbuffered queries can start processing data as soon as the first row is available, potentially reducing the time waiting for the entire result set to load.
- **Continuous Stream**: The data is continuously streamed from the database, which can be advantageous in certain use cases.
