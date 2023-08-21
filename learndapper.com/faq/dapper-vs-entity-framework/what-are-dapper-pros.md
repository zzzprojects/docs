---
id: c989377c-27cb-4293-b313-5e16d91edce8
position: 7
title: What are Dapper Pros?
---

- **Performance**: Dapper has a reputation for being one of the fastest micro-ORMs available for .NET. It provides significant performance gains over Entity Framework, especially when large amounts of data are queried.
- **Flexibility**: Dapper gives developers more control over their SQL queries, which can be a substantial advantage when you need to optimize certain queries or use more advanced SQL features that aren't supported by higher-level ORMs.
- **Ease of Use**: Despite providing lower-level access to your database, Dapper is still very easy to use and doesn't require a steep learning curve.
- **Lightweight**: Dapper is a micro-ORM and is considerably less resource-intensive than more feature-rich ORMs like Entity Framework. It's a great choice for applications where performance is critical.
- **Simplicity**: Dapper's simplicity can be seen in its API design, making it easy to understand and implement in projects.
- **Integration with Stored Procedures**: Dapper works seamlessly with stored procedures and doesn't impose any of its conventions, unlike Entity Framework. It can map the results from a stored procedure to a strongly typed model or a dynamic object.
- **Batch Queries and Transactions**: Dapper allows you to execute batch queries enabling more efficient data operations easily.
- **No Tracking of Database Objects**: Dapper doesn't track database objects, which simplifies coding for developers who prefer to manage the database state manually.
- **Efficient Data Reader Mapping**: Dapper allows for efficient mapping of .NET data reader to a specified object model. 