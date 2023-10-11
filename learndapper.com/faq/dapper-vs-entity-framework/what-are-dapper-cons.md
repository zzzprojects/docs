---
id: f33e4328-6bcd-4607-83f8-b80e4b577dac
position: 8
title: What are Dapper Cons?
---

- **Lack of Database Abstraction**: Dapper provides less abstraction from the database compared to Entity Framework. Developers have to write raw SQL queries, which might not be ideal in scenarios where you'd prefer database-agnostic code.
- **No Change Tracking**: Unlike Entity Framework, Dapper does not support change tracking. That means you'll have to manage updates to your entities manually and write explicit update statements.
- **Less Features**: As a micro-ORM, Dapper is not as feature-rich as Entity Framework. It doesn't provide out-of-the-box features like lazy loading, change tracker, migrations, etc.
- **No LINQ Support for Queries**: It does not support LINQ to build SQL queries. That can be a disadvantage if you prefer LINQ's strong typing over writing SQL strings.
- **More SQL Knowledge Required**: Given that you'll be writing raw SQL queries, your team will need to be proficient in SQL, including understanding how to avoid [SQL Injection](https://www.learndapper.com/parameters#what-is-sql-injection) attacks.
- **Database Schema Updates**: Unlike Entity Framework, which can update the database schema through migrations, Dapper lacks this feature. That could be a disadvantage when business logic changes require database schema changes.
- **Lack of a Full Query Generator**: Dapper does not have a full query generator, which could lead to writing more SQL for CRUD operations compared to Entity Framework.