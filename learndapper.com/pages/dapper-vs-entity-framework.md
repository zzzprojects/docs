---
title: Dapper vs Entity Framework (EF6 or EF Core)
description: TODO
canonical: /dapper-vs-entity-framework
status: Published
lastmod: 2023-11-17
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

## Conclusion

Choosing between Dapper and EF Core depends on your team's expertise,  company preference, and project requirements. If your team is proficient in SQL, Dapper might be the best choice. It's fast and provides control over database interactions but lacks some of the features offered by EF Core. 

EF Core is a robust ORM that abstracts database complexity. It's suitable for those favoring a model-first approach or needing additional features like migrations and identity management. Still, it might not match Dapper in performance, even if way faster than EF6.

As we have said, both ORMs are amazing and can work with any kind of project, from small-scale to massive enterprise-level applications.

## Related Articles

- [EF Core Tutorial](https://www.learnentityframeworkcore.com/)
