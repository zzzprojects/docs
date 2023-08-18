---
title: Dapper vs Entity Framework (EF6 or EF Core)
description: TODO
canonical: /dapper-vs-entity-framework
status: Published
lastmod: 2023-08-01
---

# Dapper vs Entity Framework (EF6 or EF Core)

This article will help you better understand the difference between Dapper and Entity Framework and how to choose which ORM you should use in your next project. For Entity Framework, we will mainly use Entity Framework Core (`EF Core`) as our example, but for most cases, Entity Framework 6 (`EF6`) could have been used for the same comparison as well.

This article will help you to understand:

- [What is the difference between Dapper and Entity Framework and what really matters?](#what-is-the-difference-between-dapper-and-entity-framework-and-what-really-matters)
- [How to choose the best ORM for a personal project?](#how-to-choose-the-best-orm-for-a-personal-project)
- [How to choose the best ORM for an enterprise project?](#how-to-choose-the-best-orm-for-an-enterprise-project)

## What is the difference between Dapper and Entity Framework and what really matters?

Many existing articles or posts you can find compare ORM over performance, complexity, flexibility, and a few other criteria. These are nice `keywords`, and while these factors have their importance, they often make no difference at the end of the day when choosing which ORM to use. Here are some article examples if you are still looking for those comparisons:

- [Dapper vs Entity Framework Core](https://www.c-sharpcorner.com/article/dapper-vs-entity-framework-core/)
- [Dapper vs EF Core](https://levelup.gitconnected.com/dapper-vs-ef-core-which-orm-framework-should-you-choose-for-your-net-application-54f2723b176a)

### So what really matters?

What matter is if you prefer to have control and write your own SQL queries or give control to the ORM and use LINQ to write SQL for you.

You might or not agree to make the comparison so simple, but that is the major difference between both libraries and the main reasons why people will choose one or another:

- **Dapper**: Give you full control over the SQL generated / uses stored procedures for everything.
- **Entity Framework**: Allow you to code with `LINQ` and forget everything about SQL.

Surely there is some way to use `LINQ` with Dapper and write SQL in Entity Framework. But your choice should be the 95% of the code you will write and not about the 5% exception.

For 95% of the code you will create, both ORMs are amazing and can work with any kind of project, from small-scale to massive enterprise-level applications. The remaining 5% are exception cases and should be treated as exceptions. For example, both libraries will have bad performance if you need to import 1 million customers in your database, but for this scenario, you can easily find solutions outside of those ORMs:

- [Dapper Plus Bulk Extensions](https://dapper-plus.net/bulk-insert)
- [EFE Bulk Extensions](https://entityframework-extensions.net/bulk-extensions)
- [SqlBulkCopy](https://riptutorial.com/sqlbulkcopy/learn/100000/getting-started)

So again, do you want to write all your SQL statements in Dapper or let Entity Framework generate them for you?

## How to choose the best ORM for a personal project?

When choosing an ORM for a personal project, you just have to think only about yourselves and focus on what you want to use at this moment:

- Which ORM do you prefer or perhaps want to try? Both work, no matter the project.
- Which ORM your enterprise is using? Perhaps even if you prefer one, you want to perform better in the other as your enterprise uses it.

For a personal project, there is no wrong choice. Sure, you can have coded 50h and find out that you finally prefer using EF Core over Dapper, and so what? It's a personal project, so either suck it up and use EF Core in your next project or take some time to redo all the code with EF Core. In both ways, you will survive and learn something.

For a personal project, you probably already know deep down which one you want to use, so go ahead since this is all about you and only you.

## How to choose the best ORM for an enterprise project?

When choosing an ORM for an enterprise project, you should always prioritize what is best for your team and enterprise over your personal preference.

So how to choose? There are currently 3 situations:

1. Your company is already familiar with only one of those ORMs:
   - If your company is way more familiar with EF Core than Dapper, then continue with EF Core.
   - If your company is way more familiar with Dapper than EF Core, then continue with Dapper.
2. Your company use both ORM:
   - If your company wants to choose only one, make people agree and suck it up with whatever the outcome.
   - If your company doesn't want to choose only one, use the one you prefer or more suited for the project. Someone needs to choose, after all!
3. Your company is not familiar with any of these two ORM but want to try:
   - If your company forces you to use stored procedure for everything, Dapper is a better choice (You will not be able to use EF Core to its full potential).
   - Make people agree and suck it up with whatever the outcome.
 
If your company is familiar with one ORM, stick with it. There is already enough library to learn, and there is no point in adding some eventual conflict about which ORM you should use on every project, as half of your team will prefer one and the other half the other. Even more, your developer will be more comfortable working on any project as you keep using the same way to code.

If your company already uses both, the decision is more difficult. You either make the company choose one so it doesn't have to ask this question for every project, or you choose which one is the best for this project as the answer might be different from one project to another.

If your company doesn't use any but wants to start to use only one, then in this situation, in a few scenarios such as `stored procedure`, the answer could be obvious, but in other scenarios, that should be a team decision that you stick with it.

One last situation would be simply to force the one you prefer and go to war, so it will now be the ORM your company will use. Most people will say this is a bit of very bad advice, but after all, you are only a number in a company, so why not just enjoy the time while you are there?

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-is-my-personal-jonathan-magnan-preference" itemprop="name">What is my personal (Jonathan Magnan) preference?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

I prefer Dapper when reading data and [Dapper Plus](https://dapper-plus.net/) when saving data over EF Core. The combination of both libraries makes it perfect and gives me the best flexibility and performance.

Dapper gives me 100% control over how I select my data. When I select data, I can choose all information in one database roundtrip and make sure only the information I need is selected.

Dapper Plus ensures that my data are inserted, updated, or deleted the fastest way possible and doesn't force me to create those SQL statements. The custom mapping makes it very easy to map my properties to columns without worrying about writing my SQL.

It's a little bit contradicting as I want 100% control of the SQL when reading data and no control when saving, but at the same time:
- When reading data, 95% of my code requires writing custom SQL
- When saving data, 95% of my code doesn't require any custom SQL

Another important note:

- I mostly create personal projects such as [ZZZ Code AI](https://zzzcode.ai/)
- I don't have to handle a team. Only a few developers touch with me these projects

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="can-i-use-dapper-and-ef-core-in-the-same-project" itemprop="name">Can I use Dapper and EF Core in the same project?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Yes, you can use Dapper and EF Core in the same project but keep in mind that EF Core is complex, and Dapper is very simple to use, which makes a huge difference:

- If you already use EF Core, you can easily introduce Dapper to map results to entities for some special scenarios.
- If you already use Dapper, you should not introduce EF Core as it will make the project more complex.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-orm-is-the-most-performant-between-dapper-and-ef-core" itemprop="name">Which ORM is the most performant between Dapper and EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Dapper is generally more performant than Entity Framework Core. As a micro-ORM, Dapper has less overhead and can quickly execute database operations. However, the difference in performance may not be significant for small to medium-sized applications or in scenarios where advanced features of EF Core are not heavily used. As always, the best choice depends on the specific requirements and constraints of your project.

Even if Dapper is the most performant, you will not notice the difference in most cases.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-orm-is-the-easiest-to-learn-between-dapper-and-ef-core" itemprop="name">Which ORM is the easiest to learn between Dapper and EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Dapper is easier to learn. As a micro-ORM, it has fewer features and a more straightforward API, resulting in a rapid learning curve. However, Dapper also requires developers to write raw SQL queries, which might be an additional thing to learn if you are unfamiliar with SQL.

On the other hand, EF Core is harder to learn but provides many advanced features and abstractions. On the plus side, you don't have to learn SQL, which can make learning easier for people without only a little SQL knowledge.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="which-orm-is-the-easiest-to-use-between-dapper-and-ef-core" itemprop="name">Which ORM is the easiest to use between Dapper and EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Both are easy to use when you are familiar with the ORM. 

When starting, Dapper will be easiest to use over EF Core as you can quickly understand the API over EF Core.

However, EF Core might be considered easiest to use for those who prefer to work with LINQ and higher-level abstractions. 

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-advantage-dapper-have-over-entity-framework-core" itemprop="name">What advantage Dapper have over Entity Framework Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Performance**: Dapper is typically more performant than EF Core due to its lightweight nature and lack of overhead.
- **Control over SQL Queries**: With Dapper, you have full control over your SQL queries, which can lead to more optimized database operations.
- **Simplicity**: Dapper's simple and straightforward design makes it easier to learn and use, especially for developers already comfortable with SQL.
- **Efficient Data Reader Mapping**: Dapper enables efficient mapping from a .NET data reader to a specific object model.
- **Flexibility with Stored Procedures**: Dapper works seamlessly with stored procedures, unlike EF Core which can impose certain conventions.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-dapper-pros" itemprop="name">What are Dapper Pros?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Performance**: Dapper has a reputation for being one of the fastest micro-ORMs available for .NET. It provides significant performance gains over Entity Framework, especially when large amounts of data are queried.
- **Flexibility**: Dapper gives developers more control over their SQL queries, which can be a substantial advantage when you need to optimize certain queries or use more advanced SQL features that aren't supported by higher-level ORMs.
- **Ease of Use**: Despite providing lower-level access to your database, Dapper is still very easy to use and doesn't require a steep learning curve.
- **Lightweight**: Dapper is a micro-ORM and is considerably less resource-intensive than more feature-rich ORMs like Entity Framework. It's a great choice for applications where performance is critical.
- **Simplicity**: Dapper's simplicity can be seen in its API design, making it easy to understand and implement in projects.
- **Integration with Stored Procedures**: Dapper works seamlessly with stored procedures and doesn't impose any of its conventions, unlike Entity Framework. It can map the results from a stored procedure to a strongly typed model or a dynamic object.
- **Batch Queries and Transactions**: Dapper allows you to execute batch queries enabling more efficient data operations easily.
- **No Tracking of Database Objects**: Dapper doesn't track database objects, which simplifies coding for developers who prefer to manage the database state manually.
- **Efficient Data Reader Mapping**: Dapper allows for efficient mapping of .NET data reader to a specified object model. 

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-dapper-cons" itemprop="name">What are Dapper Cons?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Lack of Database Abstraction**: Dapper provides less abstraction from the database compared to Entity Framework. Developers have to write raw SQL queries, which might not be ideal in scenarios where you'd prefer database-agnostic code.
- **No Change Tracking**: Unlike Entity Framework, Dapper does not support change tracking. That means you'll have to manage updates to your entities manually and write explicit update statements.
- **Less Features**: As a micro-ORM, Dapper is not as feature-rich as Entity Framework. It doesn't provide out-of-the-box features like lazy loading, change tracker, migrations, etc.
- **No LINQ Support for Queries**: It does not support LINQ to build SQL queries. That can be a disadvantage if you prefer LINQ's strong typing over writing SQL strings.
- **More SQL Knowledge Required**: Given that you'll be writing raw SQL queries, your team will need to be proficient in SQL, including understanding how to avoid [SQL Injection](/parameters#what-is-sql-injection) attacks.
- **Database Schema Updates**: Unlike Entity Framework, which can update the database schema through migrations, Dapper lacks this feature. That could be a disadvantage when business logic changes require database schema changes.
- **Lack of a Full Query Generator**: Dapper does not have a full query generator, which could lead to writing more SQL for CRUD operations compared to Entity Framework.

</div></div>
</details>


<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-advantage-entity-framework-core-has-over-dapper" itemprop="name">What advantage Entity Framework Core have over Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Fully Featured ORM**: EF Core provides a rich set of features such as lazy loading, change tracking, migrations, and more.
- **Database Abstraction**: EF Core abstracts the database system, allowing you to switch between different databases with minimal code changes.
- **Code First Development**: EF Core supports code first approach, where you define your database structure using C# classes.
- **Migration**: EF Core includes support for data migrations, allowing for database schema changes directly from the code.
- **LINQ Support**: With EF Core, you can use Language Integrated Query (LINQ) to build queries in C#, leading to more maintainable and readable code.
- **Change Tracking**: EF Core automatically tracks changes made to your entities and can update the database accordingly, eliminating the need for manual update statements.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-entity-framework-core-pros" itemprop="name">What are Entity Framework Core Pros?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Database Agnostic**: EF Core is database-agnostic, which means you can switch between different databases (like SQL Server, MySQL, SQLite, etc.) without changing much of your code.
- **Fully Featured ORM**: As a fully-featured ORM, EF Core provides out-of-the-box support for complex operations like lazy loading, change tracking, transactions, migrations, and identity management.
- **LINQ Support**: EF Core supports Language Integrated Query (LINQ), allowing developers to write queries in C# instead of SQL, which results in more readable and maintainable code.
- **Model-First Development**: EF Core supports model-first development, where you define your database structure using C# classes. That can help keep your database structure in sync with your code.
- **Change Tracking**: EF Core automatically tracks changes made to your entities and can update the database accordingly, eliminating the need for explicit update statements.
- **Data Migration**: EF Core includes robust support for migrations, allowing developers to handle database schema changes directly from the code.
- **Rich Querying Capabilities**: EF Core supports complex querying, including filtering, sorting, grouping, joining, and eager loading, without needing to write complex SQL queries.
- **Concurrency Control**: EF Core has built-in support for optimistic concurrency control, which helps resolve conflicts when multiple users update the same data simultaneously.
- **Unit Testing**: EF Core makes it easier to write unit tests for your database code. You can create an in-memory database for your unit tests to interact with, ensuring tests run quickly without impacting your real database.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-are-entity-framework-core-cons" itemprop="name">What are Entity Framework Core Cons?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

- **Performance**: EF Core can be slower than Dapper, especially when dealing with complex queries or large amounts of data due to its overhead of features and abstractions.
- **Learning Curve**: EF Core, being a fully-featured ORM, is complex and has a steeper learning curve compared to Dapper. Understanding and effectively using all its features requires time and experience.
- **Overhead**: The abstraction and additional features of EF Core come with a performance cost. The overhead can lead to slower response times compared to Dapper, which is more lightweight and faster.
- **Less Control Over SQL Generation**: EF Core generates SQL queries automatically, which can be an issue if you need fine-tuned control over your SQL queries for optimization purposes. 
- **Database Provider Limitations**: Although EF Core is database agnostic, not all features are supported equally by all database providers. Some features might work well with one database but not with others.
- **Lazy Loading Pitfalls**: While EF Core supports lazy loading, it can lead to performance issues if not used correctly. Unintended lazy loading can result in multiple round trips to the database, slowing down your application.
- **Memory Usage**: Due to its rich feature set and abstractions, EF Core can use more memory than micro ORMs like Dapper.
- **Bulk Operations**: EF Core is not optimized for bulk operations. Tasks like inserting or updating large amounts of data can be slow and resource-intensive compared to Dapper or other libraries designed for such tasks.

</div></div>
</details>

</div>

## Conclusion

Choosing between Dapper and EF Core depends on your team's expertise,  company preference, and project requirements. If your team is proficient in SQL, Dapper might be the best choice. It's fast and provides control over database interactions but lacks some of the features offered by EF Core. 

EF Core is a robust ORM that abstracts database complexity. It's suitable for those favoring a model-first approach or needing additional features like migrations and identity management. Still, it might not match Dapper in performance, even if way faster than EF6.

As we have said, both ORMs are amazing and can work with any kind of project, from small-scale to massive enterprise-level applications.

## Related Articles

- [EF Core Tutorial](https://www.learnentityframeworkcore.com/)
