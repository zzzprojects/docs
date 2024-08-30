---
PermaID: 1000213
Title: Dapper - An Overview of Third-Party Libraries
MetaDescription: Discover third-party libraries to perform CRUD operations, Bulk Operations, mapping, and use dapper through the `IDbTransaction` interface. Learn about their limitations and how to install them with NuGet.
LastMod: 2024-08-30
---

# Dapper: An Overview of Third-Party Libraries

In this article will get a quick overview of all the most popular third-party libraries that you can use with `Dapper`.

You can also find more library on the [Awesome Dapper List](https://github.com/zzzprojects/awesome-dapper)

## Why should you use a Third Party Library?

Dapper is a King of Micro ORM in terms of performance when querying data, but a lot of essential features/helpers are missing for some application scenarios.

Let's say, for example, you want to quickly updates thousands of entities at once. You can sure create your SQL and logic, but you can also use a library that has been exactly built for this ([Dapper Plus](https://dapper-plus.net/)), which also contains hundreds of options to customize your update statement the way you want.

Using a third-party library has several benefits, including the following:

- You save development time (using code already done instead of doing it)
- The code is robust (often used already by thousands of developers)
- The code has previously been thoroughly tested.
The library owner does the maintenance (if still supported!)

However, there are some downsides as well:

- Your code depends on the library
- If you have a bug or need help, you will not always have the support you want

There are many more advantages/disadvantages but using a third-party library or not is a lot of personal choice. Some developers prefer to control their code, while others prefer to keep their code simple and simply use tools built for their requirements.

## Third-Party Library Overview

Here is a list of libraries that you can use with `Dapper`:

| Library | Type | Support | Documentation | Features |
| :------ | :---------- | :------: | :------------ | :------- |
| [Dapper Plus](dapper-plus-third-party-library) | PRO | < 1 Day | [Yes](https://dapper-plus.net/tutorials) | Bulk Insert<br>Bulk Delete<br>Bulk Update<br>Bulk Merge<br>Bulk Action Async<br>Bulk Also Action<br>Bulk Then Action |
| [Dapper Transaction](dapper-plus-third-party-library) | Free | < 1 Day | [Yes](https://github.com/zzzprojects/Dapper.Transaction) | `IDbTransaction` extension methods for Dapper: A high performance Micro-ORM supporting SQL Server, MySQL, MariaDB, SQLite, SqlCE, Firebird etc..  |
| [Dapper.Contrib](dapper-contrib-third-party-library) | Free | 2 - 3 Days | [Yes](https://github.com/StackExchange/Dapper/tree/master/Dapper.Contrib) | Get<br>GetAll<br>Insert<br>Update<br>Delete<br>DeleteAll |
| [DapperExtensions](dapper-extensions) | Free | No | No | Get<br>GetList<br>Insert<br>Update<br>Delete<br>Count |
| [Dapper.FastCrud](dapper-fastcrud) | Free | < 1 Day | [Yes](https://github.com/MoonStorm/Dapper.FastCRUD/wiki) | Get<br>Find<br>Insert<br>Update<br>BulkUpdate<br>Delete<br>BulkDelete<br>Count |
| [Dapper.FluentMap](dapper-fluentmap) | Free | 2 - 5 Days | [Yes](https://github.com/henkmollema/Dapper-FluentMap) | Manual mapping<br>Convention based mapping |
| [Dapper.Mapper](dapper-mapper) | Free | No | No | Multi-mapping |
| [Dapper.Rainbow](dapper-rainbow) | Free | 2 - 3 Days | No | Get<br>Insert<br>Update<br>Delete<br>All |
| [Dapper.SimpleCRUD](dapper-simplecrud) | Free | 1 - 3 Days | [Yes](https://github.com/ericdc1/Dapper.SimpleCRUD) | Get<br>GetList<br>GetListPaged<br>Insert<br>Update<br>Delete<br>DeleteList<br>RecordCount |
| [Dapper.SimpleSave](dapper-simplesave) | Free | No | [Yes](https://github.com/Paymentsense/Dapper.SimpleSave/wiki/) | Create<br>CreateAll<br>Update<br>UpdateAll<br>Delete<br>DeleteAll<br>SoftDelete<br>SoftDeleteAll |

## Related Articles

- [Awesome Dapper List](https://github.com/zzzprojects/awesome-dapper)