---
title: Entity Framework Extensions
description: An introduction to Entity Framework Extensions
canonical: /extensions/entity-framework-extensions
status: Published
lastmod: 2025-11-11
---

# Entity Framework Extensions

> **TL;DR:** Entity Framework Extensions is the recommended library for professionals who need the fastest performance, reliable support, and complete documentation.

[Entity Framework Extensions](https://entityframework-extensions.net/) is the most downloaded library for developers looking for high-performance bulk operations backed by professional support — with **over 55 million downloads** worldwide.

Developed and maintained by [ZZZ Projects](https://zzzprojects.com/) — a leading .NET software company since 2014 — this library, often called **"EF Extensions"** or **"EFE"**, includes **hundreds of features**, **monthly updates**, and **dedicated support** through GitHub and email.

It supports all major versions of Entity Framework, including **EF6** and **EF Core 2 to EF Core 10**, with continuous updates to ensure compatibility.

Trusted by **thousands of .NET developers worldwide**, it has become the go-to choice for teams that need both **speed** and **reliability**.

You can explore the [professional documentation](https://entityframework-extensions.net/bulk-extensions) to learn more about bulk operations and discover advanced use cases.

---

## Code Examples

You can use **Entity Framework Extensions (EFE)** to perform all your bulk operations with just a few lines of code.

Here’s a quick overview showing how simple and powerful it is:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// --- Saving Operations ---
context.BulkInsert(customers); // Fast insert
context.BulkInsert(customers, options => { options.IncludeGraph = true; }); // Insert with related entities
context.BulkUpdate(customers); // Fast update
context.BulkDelete(customers); // Fast delete
context.BulkMerge(customers);  // Insert or update (Upsert)
context.BulkSynchronize(customers); // Keep table in sync with your data
context.BulkSaveChanges(); // Replace SaveChanges() with a faster version

// --- Retrieval Operations ---
context.Customers.BulkRead(deserializedCustomers); // Retrieve entities using a filtered query
context.Customers.WhereBulkContains(deserializedCustomers).ToList(); // Filter the query to include matching items
context.Customers.WhereBulkNotContains(deserializedCustomers).ToList(); // Filter the query to exclude matching items
context.Customers.WhereBulkContainsFilterList(deserializedCustomers).ToList(); // Return entities from the list that exist in the database
context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers).ToList(); // Return entities from the list that don't exist in the database
```

These methods work seamlessly with your existing `DbContext` — no special setup required.

You can start small with one operation and easily extend it as your project grows.

To explore and run these examples interactively, visit the [**Online Examples page**](https://entityframework-extensions.net/online-examples).


---

## Key Highlights

### ✅ Pros

* ⚡ **Exceptional performance** — up to **95% faster** than `SaveChanges()` for both simple and complex bulk operations.
* 🧩 **Seamless integration** — works out of the box with your existing EF Core entities, relationships, and configurations.
* ⚙️ **Hundreds of options** — fine-tune every operation (batch size, keys, transactions, formulas, temporary tables, and more).
* 🧠 **Supports complex models** — handles deep graphs, owned types, navigation properties, and inheritance without issues.
* 🗄️ **Database agnostic** — compatible with major providers like **SQL Server**, **PostgreSQL**, **MySQL**, **MariaDB**, **SQLite**, and **Oracle**.
* 🔁 **Actively maintained** — frequent updates ensure compatibility from **EF6** to **EF Core 2–9**, with ongoing support for future versions.
* 💬 **Professional support** — get direct access to experienced developers through GitHub and email for fast and reliable assistance.

### ⚠️ Cons

* 💰 **Requires a license for production use** — while a commercial license is needed for deployed projects, an unlimited **trial version** is available for testing, learning, and evaluating all features risk-free.

---

## 🧪 Benchmarks

✅ **Entity Framework Extensions delivers outstanding performance gains in every scenario** — whether you’re inserting thousands of entities, updating existing data, or performing complex merge operations.

The `IncludeGraph` feature works seamlessly with the `BulkInsert`, `BulkUpdate`, `BulkDelete`, and `BulkMerge` methods, allowing you to process entire entity graphs efficiently.

You can view or reproduce all benchmark results directly in our [GitHub repository](https://github.com/zzzprojects/learnentityframeworkcore/tree/main/benchmarks/Z.EntityFramework.Extensions.EFCore).

### Bulk Insert

![Benchmark EFCore vs Entity Framework Extensions – SQL Server - Bulk Insert](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/Z.EntityFramework.Extensions.EFCore/benchmark-result/bulk-insert.png)

### Bulk Update

![Benchmark EFCore vs Entity Framework Extensions – SQL Server - Bulk Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/Z.EntityFramework.Extensions.EFCore/benchmark-result/bulk-update.png)

### Bulk Merge (“Upsert” / “InsertOrUpdate”)

![Benchmark EFCore vs Entity Framework Extensions – SQL Server - Bulk Merge](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/Z.EntityFramework.Extensions.EFCore/benchmark-result/bulk-merge.png)

### More Benchmarks

Entity Framework Extensions also provides additional benchmark projects and detailed comparisons for developers who want to explore more results.

#### 📊 By Provider (EF Core)

* [SQL Server](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlserver.md)
* [PostgreSQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-postgresql.md)
* [MySQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mysql.md)
* [MariaDB](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mariadb.md)
* [Oracle](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-oracle.md)
* [SQLite](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlite.md)

#### ⚙️ By Operation (EF Core)

* [Bulk Insert](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-insert.md)
* [Bulk Update](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-update.md)
* [Bulk Delete](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-delete.md)
* [Bulk Merge](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-merge.md)
* [Bulk SaveChanges](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-savechanges.md)
* [Bulk Synchronize](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-synchronize.md)


---

## ❓ FAQ

### 🧪 How can I use the monthly trial?

To use the **Entity Framework Extensions trial**, simply **download the latest version from [NuGet](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/)** and start coding — no registration required.

Each new release (published once per month) automatically refreshes the trial period. Just update the package to the latest version to continue your evaluation.

The trial includes **all features**, allowing you to fully test and explore the library before deciding to purchase a license.

---

### 💡 Is the license perpetual or subscription-based?

The **Entity Framework Extensions** license type is **perpetual**, meaning you purchase it once and can use it forever.

Renewing **support and upgrades** is **optional**, but highly recommended if you want to access the latest features, improvements, and performance updates.

---

### 💰 Can Entity Framework Extensions be used for free?

Yes — you can use it for free by downloading the **latest trial version** each month.

However, this is **not recommended for commercial use**, since the trial period may expire before a new release becomes available.

For personal projects, learning, or testing, you can continue using the trial as long as you want without any restrictions.

---

### ⚙️ Which EF Core versions are supported?

**Entity Framework Extensions** supports all major versions — from **EF Core 2** up to **EF Core 10**.

It also supports **Entity Framework 6 (EF6)** for legacy or existing projects.

For more details and download options, visit the [official download page](https://entityframework-extensions.net/download).

---

### 🧩 Which version of Entity Framework Extensions should I use?

Each version of **Entity Framework Extensions** is designed to match the corresponding **EF Core major release** for the best compatibility and performance.

Here’s a quick guide:

* For **EF Core 10.x**, use **EF Extensions v10.x**
* For **EF Core 9.x**, use **EF Extensions v9.x**
* For **EF Core 8.x**, use **EF Extensions v8.x**
* For **EF Core 7.x**, use **EF Extensions v7.x**
* For **EF Core 6.x**, use **EF Extensions v6.x**
* For **EF Core 5.x**, use **EF Extensions v5.x**
* For **EF Core 3.x**, use **EF Extensions v3.x**
* For **EF Core 2.x**, use **EF Extensions v2.x**

Each release of **EF Extensions** is actively maintained for its corresponding EF Core version to ensure **full feature compatibility** and **optimal performance**.

---

### 🗄️ Which databases are supported?

**Entity Framework Extensions** supports all major relational databases used with EF Core, ensuring consistent behavior and performance across providers.

Supported databases include:

* **SQL Server**
* **PostgreSQL**
* **MySQL**
* **Oracle**
* **SQLite**
* **MariaDB**

---

### 🔁 Is Entity Framework Extensions still maintained?

Yes — **Entity Framework Extensions** is **actively maintained** and receives **monthly updates** that include bug fixes, performance improvements, and new features.

Developed and supported **since 2014**, the library has evolved alongside every major EF Core release and is trusted by **thousands of developers and companies worldwide**.

You can get **direct support** on **[GitHub Issues](https://github.com/zzzprojects/EntityFramework-Extensions/issues)** and by **email**.

---

### 🚀 What kind of operations can it accelerate?

Entity Framework Extensions provides **high-performance alternatives** for common EF Core operations, allowing you to process thousands — or even millions — of records efficiently.

Here are some of the most popular methods:

* [BulkInsert](https://entityframework-extensions.net/bulk-insert)
* [BulkInsertOptimized](https://entityframework-extensions.net/bulk-insert-optimized)
* [BulkUpdate](https://entityframework-extensions.net/bulk-update)
* [BulkDelete](https://entityframework-extensions.net/bulk-delete)
* [BulkMerge (Upsert)](https://entityframework-extensions.net/bulk-merge)
* [BulkSynchronize](https://entityframework-extensions.net/bulk-synchronize)
* [BulkSaveChanges](https://entityframework-extensions.net/bulk-savechanges)

In addition, the library offers **powerful data retrieval extensions**, such as:

* [BulkRead](https://entityframework-extensions.net/bulk-read)
* [WhereBulkContains](https://entityframework-extensions.net/where-bulk-contains)
* [WhereBulkNotContains](https://entityframework-extensions.net/where-bulk-not-contains)
* [WhereBulkContainsFilterList](https://entityframework-extensions.net/where-bulk-contains-filter-list)
* [WhereBulkNotContainsFilterList](https://entityframework-extensions.net/where-bulk-not-contains-filter-list)

These methods go beyond EF Core’s built-in capabilities — giving you speed, flexibility, and full control over how your data is processed and retrieved.

---

### 🧠 How hard is it to integrate?

Integration is simple — you just install the **[NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/)**, and your existing EF Core entities and configurations will work immediately.

There’s no need to change your existing mappings or database schema.

---

### 🧾 Can it be used in production?

Absolutely. Entity Framework Extensions is trusted by **thousands of .NET developers and companies worldwide**, from small startups to large enterprises.

It’s built for reliability, scalability, and long-term support.

---

## 🧭 Summary

Entity Framework Extensions isn’t just another EF Core library — it’s the **#1 performance upgrade** for your data access layer.
With just a few lines of code, you can make your operations run **up to 95% faster**, reduce memory usage, and handle **millions of records** effortlessly.

Trusted by **thousands of developers and companies worldwide** since **2014**, EF Extensions has become the go-to solution for teams who want both **speed and stability** without rewriting their applications.

It includes **hundreds of options**, seamless integration with all major databases, and professional support that’s always ready to help you succeed.

If you want your EF Core projects to feel **faster, smarter, and production-ready**, there’s no reason to wait —
👉 [Try the full-featured trial today](https://entityframework-extensions.net/download) and experience the difference for yourself.