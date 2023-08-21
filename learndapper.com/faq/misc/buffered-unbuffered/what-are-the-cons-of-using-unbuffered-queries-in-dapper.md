---
id: 9763cbc3-a7e0-4f76-a3b9-0f64413eff32
position: 7
title: What are the cons of using unbuffered queries in Dapper?
---

- **Slower Access**: Since data is loaded one row at a time, it may take longer to access all the data compared to a buffered query where all the data is loaded into memory at once.
- **Connection Duration**: The connection to the database remains open until all data has been read, potentially keeping the connection open longer than with a buffered query, particularly with large result sets.
- **Complexity**: Handling unbuffered queries can be slightly more complex than handling buffered queries, particularly in scenarios where you need to manage the open connection for a longer period.
- **Partial Data**: If an error occurs midway through an unbuffered read, you may end up with only a portion of the expected data.
- **Memory Management**: While unbuffered queries are more memory-efficient for large data sets, for small data sets, the overhead of managing the "streaming" of data could potentially use more resources than simply loading all the data into memory.
