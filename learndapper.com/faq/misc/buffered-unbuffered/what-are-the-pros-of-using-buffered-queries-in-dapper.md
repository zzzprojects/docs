---
id: 0da55eb4-d573-44ca-b289-668716750fc6
position: 2
title: What are the pros of using buffered queries in Dapper?
---

- **Immediate Access**: Since all data is loaded into memory at once, it's immediately available for processing.
- **Connection Efficiency**: The database connection is closed as soon as all data is fetched, which may lead to more efficient connection use.
- **Simplicity**: Buffered queries are easier to use and handle, particularly for smaller datasets.
- **Exception Handling**: Any exceptions during data retrieval from the database will be thrown immediately during query execution.
- **Default Behavior**: It's the default mode in Dapper, so you don't need to specify any additional parameters for buffered queries.