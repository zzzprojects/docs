---
title: EFCore.BulkExtensions.MIT: Documentation, Benchmarks & Alternatives
description: Learn how EFCore.BulkExtensions.MIT works, what it supports, its limitations and benchmarks, and when to consider Entity Framework Extensions.
canonical: /extensions/efcore-bulkextensions-mit
status: Published
lastmod: 2026-08-18
---

# EFCore.BulkExtensions.MIT

> **Disclosure:** Learn Entity Framework Core is maintained by ZZZ Projects, the company behind [Entity Framework Extensions](https://entityframework-extensions.net/). We aim to keep this page factual and link to original sources so you can evaluate the available options yourself.

> **TL;DR:** [EFCore.BulkExtensions.MIT](https://github.com/videokojot/EFCore.BulkExtensions.MIT) is a community fork of EFCore.BulkExtensions distributed under the permissive MIT license. It is free for personal, open-source, and commercial projects and supports the standard bulk insert, update, delete, read, upsert, synchronize, and bulk `SaveChanges` scenarios.
>
> **MySQL users:** The `EFCore.BulkExtensions.Mit.MySql` package currently remains on EF Core 9 and does not yet support EF Core 10.
>
> It can be a good option when a free MIT-licensed library meets your requirements. Teams that need advanced graph and mapping scenarios, broader provider and EF-version support, direct professional assistance, and predictable long-term maintenance should also consider [Entity Framework Extensions](/extensions/entity-framework-extensions).

**Need advanced bulk features or direct professional support?** [Try Entity Framework Extensions for free](https://entityframework-extensions.net/download) or [discover why more than 5,000 companies trust it](https://entityframework-extensions.net/why-entity-framework-extensions).

## What Is EFCore.BulkExtensions.MIT?

EFCore.BulkExtensions.MIT was created from the last MIT-licensed version of the original [EFCore.BulkExtensions](https://github.com/borisdj/EFCore.BulkExtensions) project before that project adopted a conditional community and commercial license.

The fork continues under the MIT license and contains additional fixes and tests maintained independently from the original project. According to its documentation, fixes are propagated across its supported major versions and tests are run as part of the release process.

The fork currently provides common bulk operations such as:

* `BulkInsert`
* `BulkUpdate`
* `BulkDelete`
* `BulkRead`
* `BulkInsertOrUpdate`
* `BulkInsertOrUpdateOrDelete`
* `BulkSaveChanges`

## Installation

Install the main package when you need support for every available provider:

```powershell
Install-Package EFCore.BulkExtensions.MIT
```

Provider-specific NuGet packages are also available when you do not want the main package to reference every database provider:

| Database | NuGet package |
|---|---|
| SQL Server | `EFCore.BulkExtensions.MIT.SqlServer` |
| PostgreSQL | `EFCore.BulkExtensions.MIT.PostgreSql` |
| MySQL | `EFCore.BulkExtensions.MIT.MySql` |
| SQLite | `EFCore.BulkExtensions.MIT.Sqlite` |

The main `EFCore.BulkExtensions` NuGet package references all provider-specific packages.

## Code Examples

The MIT fork retains a familiar API for the most common operations:

```csharp
// Insert
context.BulkInsert(customers);

// Update
context.BulkUpdate(customers);

// Delete
context.BulkDelete(customers);

// Insert or update (upsert)
context.BulkInsertOrUpdate(customers);

// Synchronize by inserting, updating, and deleting
context.BulkInsertOrUpdateOrDelete(customers);
```

Asynchronous operations are also available:

```csharp
await context.BulkInsertAsync(customers);
await context.BulkUpdateAsync(customers);
await context.BulkDeleteAsync(customers);
```

Options are configured through `BulkConfig`:

```csharp
var bulkConfig = new BulkConfig
{
    SetOutputIdentity = true,
    BatchSize = 4000
};

await context.BulkInsertAsync(customers, bulkConfig);
```

## Key Advantages

* **MIT license:** Free for personal, open-source, and commercial projects.
* **High performance for large collections:** Bulk operations reduce database round trips and can significantly outperform `SaveChanges`.
* **Familiar bulk API:** The fork supports the standard insert, update, delete, read, upsert, synchronize, and bulk `SaveChanges` methods.
* **Multiple EF Core versions:** Separate package versions are available for several major EF Core releases, including EF Core 10.
* **Release testing:** The project states that its supported versions are tested as part of the release process.
* **Source available on GitHub:** Teams can inspect the implementation, run the tests, propose fixes, or maintain their own fork.

## Important Considerations

The absence of a license fee does not automatically make a library the lowest-cost option for every project. Before adopting the MIT fork, consider:

* **Community support:** Help depends on maintainer and community availability, with no guaranteed response or resolution time.
* **Release predictability:** New EF Core and provider support depends on volunteer availability and may not follow a guaranteed schedule.
* **Provider differences:** Advanced features and configurations are not identical across SQL Server, PostgreSQL, MySQL, and SQLite.
* **Graph limitations:** `IncludeGraph` is documented primarily for SQL Server, and complex or very large graphs should be benchmarked carefully.
* **Advanced mapping scenarios:** Inheritance, nested owned types, database-generated keys, custom matching, and provider-specific behavior should be reproduced with your actual model before adoption.
* **Internal maintenance:** If an unsupported edge case blocks production, your team may need to diagnose, fix, test, and maintain the change itself.

These considerations may be acceptable for personal projects, internal tools, and teams comfortable maintaining open-source dependencies. They can carry more risk for business-critical applications with strict deadlines or support requirements.

## Supported EF Core Versions

The `EFCore.BulkExtensions.MIT` NuGet package has releases for several EF Core generations, including EF Core 6, 7, 8, 9, and 10.

As of August 2026, NuGet lists `EFCore.BulkExtensions.MIT` version `10.22.0` for .NET and EF Core 10. Because the repository README contains some older version information, verify the package and provider combination you intend to use through the [NuGet version history](https://www.nuget.org/packages/EFCore.BulkExtensions.MIT/) and the [GitHub releases](https://github.com/videokojot/EFCore.BulkExtensions.MIT/releases).

> **Important:** An EF Core 10 version of the main package does not mean that every database provider supports EF Core 10. In particular, **MySQL is not currently supported with EF Core 10**, as documented in [GitHub issue #438](https://github.com/videokojot/EFCore.BulkExtensions.MIT/issues/438).

## Supported Database Providers

The MIT fork documents support for:

* SQL Server and Azure SQL
* PostgreSQL
* MySQL
* SQLite

Provider implementations differ:

* SQL Server uses `SqlBulkCopy` and SQL `MERGE` techniques.
* PostgreSQL uses binary `COPY` and `ON CONFLICT`.
* MySQL uses `MySqlBulkCopy` and `ON DUPLICATE KEY UPDATE`.
* SQLite uses SQL statements and `UPSERT` because it has no native bulk-copy API.

### MySQL and EF Core 10 Limitation

Although EFCore.BulkExtensions.MIT has an EF Core 10 release, its MySQL implementation does not currently support EF Core 10. The limitation is tracked in [issue #438](https://github.com/videokojot/EFCore.BulkExtensions.MIT/issues/438) and is related to the MySQL provider dependency.

This distinction matters when planning an upgrade: a project using SQL Server may be able to adopt EF Core 10 while the same library version cannot provide equivalent MySQL support. Teams using MySQL should remain on a compatible EF Core version, maintain their own workaround, or choose a library that already supports their required combination.

[Entity Framework Extensions](https://entityframework-extensions.net/) supports **MySQL with EF Core 10**, giving teams a supported upgrade path without waiting for this provider gap to be resolved.

Oracle is not listed among the four providers supported by the MIT fork. Entity Framework Extensions supports SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, and SQLite.

## Known Limitations and Scenarios to Validate

EFCore.BulkExtensions.MIT handles many common EF Core models, but some scenarios are provider-specific or require additional work.

| Scenario | Important consideration |
|---|---|
| `IncludeGraph` | Graph support is documented primarily for SQL Server. Complex and deeply nested graphs should be tested with realistic data volumes. |
| Large graphs | Our benchmark showed substantial performance and memory degradation as the tested graph became very large. |
| MySQL with EF Core 10 | Not currently supported. See [issue #438](https://github.com/videokojot/EFCore.BulkExtensions.MIT/issues/438). |
| MySQL output identities | The fork documentation states that `SetOutputIdentity` is not supported for MySQL. |
| Synchronization | `BulkInsertOrUpdateOrDelete` is not fully supported for SQLite or PostgreSQL and may need to be split into separate operations. |
| PostgreSQL custom matching | `UpdateByProperties` can require creating a temporary unique index, which can prevent the operation from running inside a transaction. |
| SQLite upsert identities | `BulkInsertOrUpdate` combined with automatic identity generation may require separate insert and update collections. |
| Shadow properties | Discriminator and other shadow values can require manual preparation before the bulk operation. |
| Database-generated keys | Generated key retrieval should be validated for the exact key type, provider, and operation used by your model. |

These are not reasons to reject the library automatically. They are scenarios that should be included in a proof of concept before adopting any bulk library for a production application.

## Performance Benchmarks

We benchmarked EFCore.BulkExtensions.MIT against EF Core `SaveChanges` using SQL Server.

The tested scenarios showed:

* Bulk operations were significantly faster for large, flat insert, update, and upsert workloads.
* `SaveChanges` could be faster for small collections because bulk operations have fixed initialization costs.
* The tested `IncludeGraph` operation experienced substantial performance and memory degradation with very large graphs.

Benchmark results depend on the package version, entity shape, number of properties, relationships, database, indexes, network, and hardware. These results apply to the tested environment and should not be interpreted as universal results for every version or provider.

You can inspect and reproduce the benchmark source in our [GitHub benchmark repository](https://github.com/zzzprojects/learnentityframeworkcore/tree/main/benchmarks/EFCore.BulkExtensions.MIT).

### Bulk Insert

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Insert](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert.png)

### Bulk Insert with Graph

In our tested scenario, `IncludeGraph` performance declined significantly as the graph grew. At the largest tested size, it was more than 10 times slower than `SaveChanges`.

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Insert with Graph](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-with-graph.png)

### Bulk Update

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-update.png)

### Bulk Insert or Update

![Benchmark EF Core vs EFCore.BulkExtensions.MIT – SQL Server – Bulk Insert or Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-or-update.png)

## EFCore.BulkExtensions.MIT or Entity Framework Extensions?

Both libraries improve EF Core performance through bulk operations, but they are designed for different priorities.

| Category | EFCore.BulkExtensions.MIT | [Entity Framework Extensions](/extensions/entity-framework-extensions) |
|---|---|---|
| License | Free MIT license | Perpetual commercial license |
| Best fit | Cost-sensitive projects with standard bulk requirements | Professional and business-critical applications |
| Frameworks | EF Core | EF Core and EF6 |
| Providers | SQL Server, PostgreSQL, MySQL, and SQLite; **MySQL is not currently supported with EF Core 10** | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, and SQLite, including MySQL with EF Core 10 |
| Core operations | Insert, update, delete, read, upsert, synchronize, and SaveChanges | Insert, update, delete, read, merge, synchronize, SaveChanges, and bulk-query operations |
| Graph operations | SQL Server-focused `IncludeGraph` support | Advanced and customizable `IncludeGraph` support |
| Automated testing | Project release tests across supported versions | More than 20,000 unit tests before every release |
| Support | Community support without guaranteed response times | Direct Tier-1 support, personalized examples, and proactive follow-ups |
| Releases | Community-driven schedule | Day-one major EF Core support and reliable monthly releases |
| Long-term responsibility | Your team may need to investigate and maintain unsupported scenarios | A professional team investigates customer issues and maintains compatibility |

### When EFCore.BulkExtensions.MIT May Be a Good Choice

Consider the MIT fork when:

* A free MIT license is a primary requirement.
* You need standard bulk operations for relatively straightforward models.
* Its supported providers and provider-specific limitations fit your application.
* Your team is comfortable diagnosing issues or contributing fixes when necessary.
* Community-based support is sufficient for the project.

### When Entity Framework Extensions May Be a Better Choice

Consider Entity Framework Extensions when:

* The cost of developer time and production risk matters more than avoiding a license fee.
* You need advanced graphs, inheritance, owned entities, database-generated values, custom keys, or specialized options.
* You use EF6, Oracle, MariaDB, or multiple database providers.
* Your application is business-critical and direct professional support matters.
* You need predictable monthly releases and day-one support for major EF Core versions.
* You prefer relying on more than 20,000 unit tests before every release.

## Why Professional Teams Choose Entity Framework Extensions

Entity Framework Extensions provides more than high-performance bulk methods. It gives development teams a production-ready solution backed by extensive testing, expert support, and more than a decade of continuous development.

* **Build faster with less risk:** Replace weeks of development, testing, and provider-specific code with production-ready operations.
* **Proven reliability:** More than **20,000 unit tests** run before every release.
* **Trusted in production:** More than **5,000 companies** have trusted Entity Framework Extensions since 2014.
* **Tier-1 customer support:** Get direct help from experienced Entity Framework experts, often with a personalized online example. We proactively follow up after 48 and 96 hours when necessary.
* **Long-term compatibility:** Benefit from day-one major EF Core support and reliable monthly updates.
* **Perpetual license:** Continue using the purchased version indefinitely, with optional renewals discounted by 30%–50%.

The comparison is not simply “free versus paid.” It is the cost of a license compared with the development time, maintenance responsibility, and technical risk your team would otherwise carry.

* [Discover why companies choose Entity Framework Extensions](https://entityframework-extensions.net/why-entity-framework-extensions)
* [Try Entity Framework Extensions for free](https://entityframework-extensions.net/download)
* [View Entity Framework Extensions pricing](https://entityframework-extensions.net/pricing)

## FAQ

### Is EFCore.BulkExtensions.MIT Free for Commercial Use?

Yes. The fork is distributed under the MIT license and can be used in personal, open-source, and commercial projects.

Always verify that you installed `EFCore.BulkExtensions.MIT`. The similarly named original `EFCore.BulkExtensions` package uses a conditional community and commercial license.

### Is EFCore.BulkExtensions.MIT Maintained?

The project has published versions for EF Core 10 and documents that fixes are propagated across its supported major versions. It also runs tests as part of its release process.

However, it remains a community-maintained project without guaranteed issue-response times, release dates, or commercial service levels. Review its [recent commits](https://github.com/videokojot/EFCore.BulkExtensions.MIT/commits/main/), [releases](https://github.com/videokojot/EFCore.BulkExtensions.MIT/releases), and [open issues](https://github.com/videokojot/EFCore.BulkExtensions.MIT/issues) before adopting it for a business-critical application.

### Which EF Core Versions Are Supported?

Package releases are available for several major EF Core versions, including EF Core 6 through EF Core 10. However, support varies by provider: **the MySQL implementation does not currently support EF Core 10**. Match both the package and provider versions to the EF Core version used by your application.

### Which Databases Are Supported?

The MIT fork documents support for SQL Server, PostgreSQL, MySQL, and SQLite. Feature availability and limitations vary by provider. Most importantly, MySQL is not currently supported with EF Core 10; see [issue #438](https://github.com/videokojot/EFCore.BulkExtensions.MIT/issues/438). Entity Framework Extensions supports MySQL with EF Core 10.

### Is It Faster Than SaveChanges?

Usually, yes, when processing sufficiently large collections with relatively flat entity models.

For small collections, `SaveChanges` can be faster because bulk operations have initialization and temporary-table overhead. Large graphs can also behave very differently from flat collections, so benchmark the actual application model.

### Does IncludeGraph Support Every Provider and Relationship?

No. The fork documents `IncludeGraph` primarily for SQL Server. Complex graphs, deeply nested relationships, and provider-specific models should be tested before adoption.

### What Support Is Available?

Support is community-based. Issues and pull requests can be submitted through GitHub, but response and resolution times are not guaranteed.

Entity Framework Extensions includes direct Tier-1 customer support, personalized online examples, and proactive follow-ups to ensure customer issues are resolved.

### What Is the Main Difference from Entity Framework Extensions?

EFCore.BulkExtensions.MIT prioritizes free MIT licensing and community maintenance.

Entity Framework Extensions is a commercial library focused on broader feature coverage, advanced customization, extensive automated testing, direct professional support, predictable releases, and long-term compatibility.

## Conclusion

EFCore.BulkExtensions.MIT is a legitimate option for developers who need a free MIT-licensed bulk library and whose requirements fit its supported providers, operations, and model limitations.

For production and business-critical applications, however, the license price is only one part of the decision. Developer time, advanced model support, test coverage, issue resolution, upgrade readiness, and long-term maintenance can cost far more than the software itself.

If those factors matter to your team, [Entity Framework Extensions](https://entityframework-extensions.net/) provides a production-ready alternative trusted by more than 5,000 companies since 2014.

[Try Entity Framework Extensions for free](https://entityframework-extensions.net/download)
