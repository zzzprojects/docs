---
title: EFCore.BulkExtensions: Documentation, License & Alternatives
description: Explore EFCore.BulkExtensions documentation, NuGet packages, licensing, BulkInsertAsync, BulkDeleteAsync, MySQL LOAD DATA LOCAL INFILE, and EF Core 10 support.
canonical: /extensions/efcore-bulkextensions
status: Published
lastmod: 2026-08-18
---

# EFCore.BulkExtensions

> **Disclosure:** Learn Entity Framework Core is maintained by ZZZ Projects, the company behind [Entity Framework Extensions](https://entityframework-extensions.net/). We aim to keep this page factual and link to official sources so you can evaluate the available options yourself.

> **TL;DR:** [EFCore.BulkExtensions](https://github.com/borisdj/EFCore.BulkExtensions) is a popular library that adds high-performance bulk operations to EF Core. It supports the standard insert, update, delete, read, upsert, synchronize, and bulk `SaveChanges` scenarios across several major database providers.
>
> The project uses a conditional community and [commercial license](https://codis.tech/efcorebulk). Companies with more than **$1 million in annual gross revenue** require an **annual commercial license**. Teams looking for a perpetual license, more advanced features, extensive regression testing, and direct professional support should also consider [Entity Framework Extensions](/extensions/entity-framework-extensions).
>
> **MySQL users:** The `EFCore.BulkExtensions.MySql` package currently remains on EF Core 9 and does not yet support EF Core 10.

## What Is EFCore.BulkExtensions?

EFCore.BulkExtensions adds bulk operations to `DbContext`, allowing large collections of entities to be processed more efficiently than with the standard EF Core `SaveChanges` method.

The library currently provides methods for common bulk scenarios, including:

* `BulkInsert` — Inserts a collection of entities.
* `BulkUpdate` — Updates existing entities.
* `BulkDelete` — Deletes entities in bulk.
* `BulkInsertOrUpdate` — Inserts new rows or updates matching rows.
* `BulkInsertOrUpdateOrDelete` — Synchronizes a table with the supplied collection.
* `BulkRead` — Reads database values by matching a collection of keys.
* `BulkSaveChanges` — Processes tracked changes using bulk operations.

## Installation

Install the main package to include all database providers available for your EF Core version:

```powershell
Install-Package EFCore.BulkExtensions
```

Provider-specific packages are also available when your application only uses one database:

| Database   | NuGet package                      |
| ---------- | ---------------------------------- |
| SQL Server | `EFCore.BulkExtensions.SqlServer`  |
| PostgreSQL | `EFCore.BulkExtensions.PostgreSql` |
| MySQL      | `EFCore.BulkExtensions.MySql`      |
| Oracle     | `EFCore.BulkExtensions.Oracle`     |
| SQLite     | `EFCore.BulkExtensions.Sqlite`     |

> **Important:** The EF Core 10 version of the main `EFCore.BulkExtensions` package does not reference `EFCore.BulkExtensions.MySql` because MySQL is not currently supported with EF Core 10. See [GitHub issue #1950](https://github.com/borisdj/EFCore.BulkExtensions/issues/1950).

The providers referenced by the main package therefore depend on the version you install.

## Code Examples

The API is straightforward for the most common operations:

```csharp
// Insert
context.BulkInsert(customers);

// Update
context.BulkUpdate(customers);

// Insert or update (upsert)
context.BulkInsertOrUpdate(customers);

// Synchronize by inserting, updating, and deleting
context.BulkInsertOrUpdateOrDelete(customers);

// Save tracked changes in bulk
context.BulkSaveChanges();
```

Asynchronous versions are also available:

```csharp
await context.BulkInsertAsync(customers);
await context.BulkUpdateAsync(customers);
await context.BulkDeleteAsync(customers);
```

Configuration is provided through a `BulkConfig` instance:

```csharp
var bulkConfig = new BulkConfig
{
    SetOutputIdentity = true,
    BatchSize = 4000
};

context.BulkInsert(customers, bulkConfig);
```

## BulkDeleteAsync with IQueryable

A common source of confusion is whether `EFCore.BulkExtensions.BulkDeleteAsync` can be called directly on an `IQueryable<T>`.

`BulkDeleteAsync` is an entity-based operation. You provide a collection of entities to the `DbContext`:

```csharp
await context.BulkDeleteAsync(customers);
```

It is not the same as deleting every row matched by an `IQueryable<T>` expression.

Older EFCore.BulkExtensions versions provided `BatchDeleteAsync` for query-based deletion:

```csharp
await context.Customers
    .Where(customer => customer.IsInactive)
    .BatchDeleteAsync();
```

The official project now marks its batch update and delete operations as deprecated for EF Core 8 and later because EF Core provides native [`ExecuteUpdate`](/saving/execute-update) and [`ExecuteDelete`](/saving/execute-delete) methods:

```csharp
await context.Customers
    .Where(customer => customer.IsInactive)
    .ExecuteDeleteAsync();
```

Entity Framework Extensions also provides query-based operations such as [`DeleteFromQueryAsync`](https://entityframework-extensions.net/delete-from-query), in addition to entity-based `BulkDeleteAsync`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Delete entities from a collection
await context.BulkDeleteAsync(customers);

// Delete rows matched by an IQueryable expression
await context.Customers
    .Where(customer => customer.IsInactive)
    .DeleteFromQueryAsync();
```

## Key Highlights

### Advantages

* **High performance for large collections:** Bulk operations reduce database round trips and can be significantly faster than `SaveChanges`.
* **Familiar API:** Operations are exposed as extension methods on `DbContext`.
* **Multiple providers:** SQL Server, PostgreSQL, MySQL, Oracle, and SQLite are supported.
* **Common bulk operations:** Insert, update, delete, read, upsert, synchronize, and bulk `SaveChanges` are included.
* **Source available:** The project source can be reviewed on GitHub.
* **Conditional community license:** Eligible individuals and organizations can use the library without purchasing a commercial license.

### Important Considerations

* **Commercial restrictions:** Companies earning more than $1 million in annual gross revenue must purchase a commercial license.
* **Annual licensing:** Commercial licenses are sold for a fixed duration and must be renewed to continue using the software beyond that duration.
* **Bulk-operation overhead:** For small collections, the initialization and temporary-table overhead may make regular `SaveChanges` faster.
* **Provider differences:** Some features and configurations behave differently or have limitations depending on the database provider.
* **Graph limitations:** According to the official documentation, `IncludeGraph` is intended for SQL Server and relatively simple parent-child relationships.

## License and Commercial Pricing

EFCore.BulkExtensions uses a dual-license model described by its author as **cFOSS**, or conditionally free open-source software.

Commercial licensing is required for companies with more than **$1 million in annual gross revenue**. As of August 2026, the official pricing page lists annual plans beginning at **$1,000 per year for 1–5 developers**, with higher tiers for larger teams.

Because licensing terms and prices can change, review the current details before adopting the library:

* [Official EFCore.BulkExtensions license](https://codis.tech/efcorebulk_license.html)
* [Official commercial pricing](https://codis.tech/efcorebulk)

### How Do the Long-Term Costs Compare?

Depending on the selected provider and number of developers, the initial purchase price of EFCore.BulkExtensions can be comparable to Entity Framework Extensions. The major difference is what happens afterward.

EFCore.BulkExtensions uses a **time-limited commercial license**. To continue using the library after the purchased duration, the license must be renewed. Its current pricing offers discounts of 10% for two years and 20% for four years.

Entity Framework Extensions provides a **perpetual license**. You can continue using the purchased version indefinitely, even if you do not renew. Renewals are required only when you want continued support and access to newer versions, and renewal discounts range from **30% to 50%**.

This can make Entity Framework Extensions significantly less expensive over the lifetime of a project—especially when your application remains on the same EF Core version for several years.

## Supported EF Core Versions

The official package currently supports **EF Core 10**, with version-specific packages available for several earlier EF Core releases.

The project follows the supported .NET and EF Core lifecycle. Its official documentation currently identifies EF Core 10 and EF Core 9 as the actively supported versions. Provider availability can differ; for example, the MySQL package may trail the latest EF Core version when its underlying provider has not yet been updated.

For the latest compatibility information, see the [official installation table](https://github.com/borisdj/EFCore.BulkExtensions#installation).

## Supported Database Providers

EFCore.BulkExtensions supports five major relational database providers:

* SQL Server and Azure SQL
* PostgreSQL
* MySQL
* Oracle
* SQLite

The implementation varies by provider. For example, SQL Server uses `SqlBulkCopy`, PostgreSQL uses binary `COPY`, and MySQL uses `MySqlBulkCopy`. Advanced behavior is not necessarily identical across providers, so provider-specific limitations should be reviewed before choosing the library for a complex project.

### SQL Server: BulkInsertAsync and SqlBulkCopy

On SQL Server, EFCore.BulkExtensions uses `SqlBulkCopy` to efficiently transfer rows during `BulkInsert` and `BulkInsertAsync` operations:

```csharp
await context.BulkInsertAsync(customers);
```

More complex operations can also use temporary tables and SQL `MERGE` statements. Options such as returning generated identity values may require additional processing compared with a pure `SqlBulkCopy` insert.

### PostgreSQL

The `EFCore.BulkExtensions.PostgreSql` package uses PostgreSQL binary `COPY` for loading data and `ON CONFLICT` for update and upsert scenarios.

### MySQL: LOAD DATA LOCAL INFILE

The `EFCore.BulkExtensions.MySql` package uses MySQL bulk-loading functionality for high-performance operations. MySQL may require local data loading to be enabled on both the server and the client connection.

The official EFCore.BulkExtensions documentation recommends enabling `local_infile` on the MySQL server:

```sql
SET GLOBAL local_infile = true;
```

When `LOAD DATA LOCAL INFILE` is used through MySqlConnector, the connection string must also enable `AllowLoadLocalInfile`:

```text
Server=localhost;Database=MyDatabase;User Id=root;Password=***;AllowLoadLocalInfile=true;
```

`AllowLoadLocalInfile` is disabled by default as a security precaution. Only enable it when required, use trusted data sources, and review the [MySqlConnector security guidance](https://mysqlconnector.net/troubleshooting/load-data-local-infile/).

#### EFCore.BulkExtensions MySQL and EF Core 10

> **Important:** As of August 2026, `EFCore.BulkExtensions.MySql` does not support EF Core 10.

Although the main EFCore.BulkExtensions package supports EF Core 10, its MySQL package remains on EF Core 9 because its underlying MySQL EF Core provider has not yet been updated for EF Core 10.

The limitation is tracked in [GitHub issue #1950](https://github.com/borisdj/EFCore.BulkExtensions/issues/1950). At the time of writing, the issue remains open with no assignee, milestone, or pull request.

This is a major compatibility consideration for applications that use MySQL and want to upgrade to EF Core 10. [Entity Framework Extensions](https://entityframework-extensions.net/) supports **MySQL with EF Core 10**, as well as reliable monthly releases and day-one support for major EF Core versions.

## Performance

Bulk operations generally provide their greatest benefit when processing large collections. For smaller collections, setup costs such as creating and dropping temporary tables can outweigh the savings.

The official project recommends considering regular EF Core operations for small collections and bulk operations when the number of entities becomes large enough to justify the additional setup.

### Our Reproducible Benchmarks

We benchmarked **EFCore.BulkExtensions.MIT**, a community fork, against EF Core `SaveChanges` using SQL Server.

The tests showed:

* Bulk operations were significantly faster than `SaveChanges` for large, flat insert, update, and upsert workloads.
* `SaveChanges` could be faster for small collections because bulk operations have fixed initialization costs.
* The tested `IncludeGraph` implementation experienced a significant performance and memory decline with very large graphs.

These results apply to the specific EFCore.BulkExtensions.MIT version and test environment used by the benchmark. They should not automatically be interpreted as results for every version of the official EFCore.BulkExtensions package.

You can inspect the benchmark source and reproduce the tests in our [GitHub benchmark repository](https://github.com/zzzprojects/learnentityframeworkcore/tree/main/benchmarks/EFCore.BulkExtensions.MIT).

### Bulk Insert

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Insert](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert.png)

### Bulk Insert with Graph

In our tested scenario, graph performance declined significantly as the number of related entities increased.

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Insert with Graph](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-with-graph.png)

### Bulk Update

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-update.png)

### Bulk Insert or Update

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Insert or Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-or-update.png)

## EFCore.BulkExtensions or Entity Framework Extensions?

Both libraries improve performance by processing entities in bulk, but they are designed around different licensing, feature, and support models.

| Category | EFCore.BulkExtensions | [Entity Framework Extensions](/extensions/entity-framework-extensions) |
|---|---|---|
| License | Conditional community or annual commercial license | Perpetual commercial license |
| Long-term cost | Time-limited license with continued renewals | Perpetual license with optional renewals discounted by 30%–50% |
| Frameworks | EF Core | EF Core and EF6 |
| Core operations | Insert, update, delete, read, upsert, synchronize, and SaveChanges | Insert, update, delete, read, merge, synchronize, SaveChanges, and bulk-query operations |
| Database providers | SQL Server, PostgreSQL, MySQL, Oracle, and SQLite; MySQL currently remains on EF Core 9 | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, and SQLite, including MySQL with EF Core 10 |
| Graph operations | Limited `IncludeGraph` scenarios documented primarily for SQL Server | Advanced, customizable `IncludeGraph` support |
| Automated regression testing | Review the project repository for its current test coverage | More than 20,000 unit tests run before every release |
| Support | Community and commercial support options | Direct Tier-1 support with personalized examples and proactive follow-ups |
| Releases | Follows supported EF Core and .NET versions | Day-one major EF Core support and reliable monthly releases |

### When EFCore.BulkExtensions May Be a Good Choice

Consider EFCore.BulkExtensions when:

* Your organization qualifies for its community license.
* You prefer a source-available solution.
* You need standard bulk operations for relatively straightforward entity models.
* Its provider-specific capabilities cover all your required scenarios.

### When Entity Framework Extensions May Be a Better Choice

Consider Entity Framework Extensions when:

* You need a **perpetual license** instead of an annual commercial license.
* You need advanced graph operations, custom keys, inheritance, bulk queries, or highly specialized options.
* Your application is business-critical and direct professional support matters.
* You want a library backed by more than **20,000 unit tests** before every release.
* You need predictable monthly releases and day-one support for major EF Core versions.
* You use both EF Core and EF6.

## Why Professional Teams Choose Entity Framework Extensions

Entity Framework Extensions provides more than high-performance bulk methods. It gives development teams a production-ready solution backed by extensive testing, expert support, and more than a decade of continuous development.

* **Build faster with less risk:** Replace weeks of development, testing, and provider-specific code with production-ready operations that work in only a few lines of code.
* **Proven reliability:** More than **20,000 unit tests** run before every release.
* **Trusted in production:** More than **5,000 companies** have trusted Entity Framework Extensions since 2014.
* **Tier-1 customer support:** Get direct help from experienced Entity Framework experts, often with a personalized online example. We proactively follow up to ensure your issue is fully resolved.
* **Long-term compatibility:** Benefit from day-one major EF Core support and reliable monthly updates.

You are not simply purchasing bulk methods. You are reducing development time, maintenance costs, and technical risk.

* [Discover why companies choose Entity Framework Extensions](https://entityframework-extensions.net/why-entity-framework-extensions)
* [Try Entity Framework Extensions for free](https://entityframework-extensions.net/download)
* [View Entity Framework Extensions pricing](https://entityframework-extensions.net/pricing)

## FAQ

### Is EFCore.BulkExtensions Free for Commercial Use?

It depends on your organization.

The community license is available only to organizations that meet its eligibility conditions. Companies with more than **$1 million in annual gross revenue** must purchase an annual commercial license.

Always review the [current official license](https://codis.tech/efcorebulk_license.html) before using the package in a commercial project.

### Is the Commercial License Perpetual?

No. The official commercial license grants usage rights for the purchased duration. Plans are currently offered for one, two, or four years.

By comparison, Entity Framework Extensions provides a perpetual license. The purchased version can continue to be used indefinitely, while renewals for future upgrades and support are optional.

### Is EFCore.BulkExtensions Maintained?

Yes. As of August 2026, the project has published releases for EF Core 10 and continues to receive compatibility updates, fixes, and other changes.

However, maintenance and compatibility vary by database provider. For example, MySQL is not currently supported with EF Core 10. Issue-response times and release dates are also not guaranteed through the public GitHub repository.

Before adopting the library for a business-critical application, review its [recent commits](https://github.com/borisdj/EFCore.BulkExtensions/commits/master/), [releases](https://github.com/borisdj/EFCore.BulkExtensions/releases), and [open issues](https://github.com/borisdj/EFCore.BulkExtensions/issues) to evaluate its current activity and compatibility with your provider and EF Core version.

### Is EFCore.BulkExtensions Faster Than SaveChanges?

Usually, yes—when processing sufficiently large collections.

Bulk operations reduce database round trips and use provider-specific loading techniques. For small collections, however, `SaveChanges` can be faster because bulk operations must initialize their processing and may create temporary tables.

The actual result depends on the operation, number of entities, entity shape, database provider, configuration, network, and hardware. Benchmark your real scenario before making a performance-sensitive decision.

### Which EF Core Versions Are Supported?

The latest package supports EF Core 10. Version-specific packages are available for several earlier releases, while active maintenance follows the supported .NET and EF Core lifecycle.

### Which Databases Are Supported?

EFCore.BulkExtensions supports SQL Server, PostgreSQL, MySQL, Oracle, and SQLite. Some advanced behaviors and limitations vary by provider.

### Does EFCore.BulkExtensions.MySql Support EF Core 10?

Not currently. As of August 2026, the MySQL package remains on EF Core 9 because of its provider dependency. The upgrade request is tracked in [GitHub issue #1950](https://github.com/borisdj/EFCore.BulkExtensions/issues/1950).

### Why Does MySQL Require LOAD DATA LOCAL INFILE?

MySQL bulk-loading implementations use native loading capabilities to transfer many rows efficiently. Depending on the configuration, you may need to enable `local_infile` on the MySQL server and add `AllowLoadLocalInfile=true` to the MySqlConnector connection string.

Because local file loading has security implications, it is disabled by default and should be enabled only when required with trusted data sources.

### Can BulkDeleteAsync Be Used with IQueryable?

Not directly. `BulkDeleteAsync` accepts a collection of entities. For query-based deletion, use EF Core `ExecuteDeleteAsync`, the older EFCore.BulkExtensions `BatchDeleteAsync` API where applicable, or Entity Framework Extensions `DeleteFromQueryAsync`.

### What Is the Main Difference from Entity Framework Extensions?

EFCore.BulkExtensions offers standard bulk operations through a conditional community or annual commercial license.

Entity Framework Extensions is a commercial library focused on broader feature coverage, advanced customization, extensive automated testing, direct Tier-1 support, and a perpetual license.

## Conclusion

EFCore.BulkExtensions remains a capable option for developers who qualify for its community license and whose requirements fit its supported operations and provider-specific behavior.

For professional and business-critical applications, the software price is only one part of the decision. Development time, test coverage, advanced features, support quality, upgrade readiness, and long-term licensing costs can matter much more.

If those factors are important to your team, [Entity Framework Extensions](https://entityframework-extensions.net/) provides a production-ready alternative trusted by more than 5,000 companies since 2014.

[Try Entity Framework Extensions for free](https://entityframework-extensions.net/download)
