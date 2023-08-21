---
id: ba679ed4-1e41-4e08-ba8b-ec4103303548
position: 10
title: What are Entity Framework Core Pros?
---

- **Database Agnostic**: EF Core is database-agnostic, which means you can switch between different databases (like SQL Server, MySQL, SQLite, etc.) without changing much of your code.
- **Fully Featured ORM**: As a fully-featured ORM, EF Core provides out-of-the-box support for complex operations like lazy loading, change tracking, transactions, migrations, and identity management.
- **LINQ Support**: EF Core supports Language Integrated Query (LINQ), allowing developers to write queries in C# instead of SQL, which results in more readable and maintainable code.
- **Model-First Development**: EF Core supports model-first development, where you define your database structure using C# classes. That can help keep your database structure in sync with your code.
- **Change Tracking**: EF Core automatically tracks changes made to your entities and can update the database accordingly, eliminating the need for explicit update statements.
- **Data Migration**: EF Core includes robust support for migrations, allowing developers to handle database schema changes directly from the code.
- **Rich Querying Capabilities**: EF Core supports complex querying, including filtering, sorting, grouping, joining, and eager loading, without needing to write complex SQL queries.
- **Concurrency Control**: EF Core has built-in support for optimistic concurrency control, which helps resolve conflicts when multiple users update the same data simultaneously.
- **Unit Testing**: EF Core makes it easier to write unit tests for your database code. You can create an in-memory database for your unit tests to interact with, ensuring tests run quickly without impacting your real database.