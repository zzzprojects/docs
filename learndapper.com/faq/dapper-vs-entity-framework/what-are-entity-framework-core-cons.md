---
id: ec596ae0-047f-4a1e-921a-a6b7509c4481
position: 11
title: What are Entity Framework Core Cons?
---

- **Performance**: EF Core can be slower than Dapper, especially when dealing with complex queries or large amounts of data due to its overhead of features and abstractions.
- **Learning Curve**: EF Core, being a fully-featured ORM, is complex and has a steeper learning curve compared to Dapper. Understanding and effectively using all its features requires time and experience.
- **Overhead**: The abstraction and additional features of EF Core come with a performance cost. The overhead can lead to slower response times compared to Dapper, which is more lightweight and faster.
- **Less Control Over SQL Generation**: EF Core generates SQL queries automatically, which can be an issue if you need fine-tuned control over your SQL queries for optimization purposes. 
- **Database Provider Limitations**: Although EF Core is database agnostic, not all features are supported equally by all database providers. Some features might work well with one database but not with others.
- **Lazy Loading Pitfalls**: While EF Core supports lazy loading, it can lead to performance issues if not used correctly. Unintended lazy loading can result in multiple round trips to the database, slowing down your application.
- **Memory Usage**: Due to its rich feature set and abstractions, EF Core can use more memory than micro ORMs like Dapper.
- **Bulk Operations**: EF Core is not optimized for bulk operations. Tasks like inserting or updating large amounts of data can be slow and resource-intensive compared to Dapper or other libraries designed for such tasks.