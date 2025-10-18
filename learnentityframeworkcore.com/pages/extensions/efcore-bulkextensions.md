---
title: EFCore.BulkExtensions
description: An introduction to EFCore.BulkExtensions
canonical: /extensions/efcore-bulkextensions
status: Published
lastmod: 2025-10-16
---

# EFCore.BulkExtensions

> **TL;DR:** EFCore.BulkExtensions was once a great library for bulk operations in EF Core — but it’s no longer actively maintained or commercially viable. Use the [**MIT fork**](/extensions/efcore-bulkextensions-mit) for free projects, or [**Entity Framework Extensions**](/extensions/entity-framework-extensions) for professional performance and long-term support.


The [EFCore.BulkExtensions](https://github.com/borisdj/EFCore.BulkExtensions) library was once a popular choice for performing **Bulk Inserts, Updates, Deletes, and InsertOrUpdate (upsert)** operations in Entity Framework Core (EF Core). It gained attention for being easy to use and completely free — making it one of the most downloaded bulk operation libraries for EF Core.

But why are we talking in the past tense?

Because everything changed in early **2023**, when the project adopted a new [dual-license model](https://github.com/borisdj/EFCore.BulkExtensions/issues/1079).

Since then, **commercial use has been restricted** to companies with **less than $1M in annual revenue**.

As a result, the official EFCore.BulkExtensions package is **no longer recommended**, even for non-commercial projects, because the library is **no longer actively supported** — aside from occasionally updating the **README.md** file — and most support requests **remain unanswered**.

Today, developers typically choose between two better options:

* 🧩 **[EFCore.BulkExtensions.MIT](https://github.com/videokojot/EFCore.BulkExtensions.MIT)** — a community-maintained fork with active support that remains fully open-source and free to use.
* 🚀 **[Entity Framework Extensions](https://entityframework-extensions.net/)** — a professional solution offering faster performance, more features, and long-term commercial support.

💡 **In short:** use **EFCore.BulkExtensions.MIT** if you need a reliable, free, and open-source option — or **Entity Framework Extensions** if you want professional-grade performance and support.

---

## Code Examples

```csharp
context.BulkInsert(customers);
context.BulkInsert(customers, options => { options.IncludeGraph = true; });
context.BulkUpdate(customers);
context.BulkInsertOrUpdate(customers);
```
---

## Key Highlights

### ✅ Pros

* ⚡ **Faster than `SaveChanges()`** for simple, flat operations
* 🧩 **Easy to integrate** — works with existing EF Core entities and configurations
* 🗄️ **Database agnostic** — supports multiple database providers
* 💡 **Free for personal and companies earning less than 1M/year**

### ❌ Cons

* ⚠️ **Commercial restrictions** — companies earning over **$1M/year** must purchase a license
* 💸 **Subscription-based** — requires annual renewals (not perpetual)
* 🐢 **Performance drops sharply** — when using a few entities or `IncludeGraph` with too much entities
* 💤 **Not actively maintained** — minimal GitHub activity and no issue responses
* 🚫 **No longer competitive** — the [MIT fork](/extensions/efcore-bulkextensions-mit) offers the same functionality for free
* ⚖️ **Similar price to [Entity Framework Extensions](/extensions/entity-framework-extensions)**, yet offers only a fraction of the performance and features

---

## 🧪 Benchmarks – EFCore.BulkExtensions vs EF Core

For benchmarking purposes, we compared the **MIT fork** of EFCore.BulkExtensions (since the official package now requires a paid commercial license for us) against **pure EF Core** using `SaveChanges()` with **SQL Server** as the database provider.

The results are straightforward and easy to interpret:

* ✅ **Much faster than `SaveChanges()`** for simple **Insert**, **Update**, or **InsertOrUpdate** operations when working with **large datasets**
* ⚠️ **Slightly slower than `SaveChanges()`** when processing a **small number of entities**, due to the fixed initialization overhead
* ❌ **Up to 10x slower than `SaveChanges()`** when using **`IncludeGraph`** with **around 10,000 entities or more**, making this option **effectively unusable** for performance-focused scenarios

This last point is especially important — the **`IncludeGraph` feature** performs well on smaller graphs but suffers a major performance drop with **very large datasets** due to memory usage.

You can review or reproduce these benchmark tests directly in our [GitHub repository](https://github.com/zzzprojects/learnentityframeworkcore/tree/main/benchmarks/EFCore.BulkExtensions.MIT).

### Bulk Insert

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk Insert](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert.png)

### Bulk Insert (with Graph)

> This is not a mistake — EFCore.BulkExtensions `BulkInsert` was indeed more than 10x slower than `SaveChanges()` for 100k entities.

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk Insert with Graph](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-with-graph.png)

### Bulk Update

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-update.png)

### Bulk InsertOrUpdate

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk InsertOrUpdate](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-or-update.png)

---

## ❓ FAQ

### Is EFCore.BulkExtensions free to use commercially?

No.

It’s **not free for commercial projects** if your company earns more than **$1M in annual revenue**.

This restriction excludes most professional development teams, SaaS companies, and enterprises from using it for free.

If your organization exceeds that limit, you’ll need to **[purchase a commercial license](https://codis.tech/efcorebulk)**.

---

### Is the license perpetual or subscription-based?

The EFCore.BulkExtensions license is **subscription-based**, meaning you must **renew it every year** to continue using it.

Unfortunately, this is the **worst kind of license model** — once you purchase it, you’ll need to renew it **year after year**, even if you’re no longer actively developing the project.

This approach adds **recurring costs and extra management overhead**.

💡 **In comparison:**
[**Entity Framework Extensions**](/extensions/entity-framework-extensions#is-the-license-perpetual-or-subscription-based) offers **perpetual licenses**, which you pay **only once** and can use **forever** — no annual renewals required.

---

### What’s the difference between EFCore.BulkExtensions and Entity Framework Extensions?

That’s like asking the difference between a **Lada** and a **Lamborghini**.

**Entity Framework Extensions** simply **offers more of everything** — from features to performance, stability, support, and even pricing.

What **Entity Framework Extensions** offers:

* 💡 Offers a **perpetual license** (no forced renewals)
* ⚙️ Provides **far more features, customization options, and integrations**
* 🧩 Is **actively supported** and **regularly updated**
* ⚡ Delivers **better performance across all scenarios**
* 🏆 Backed by a **professional team** ensuring long-term reliability
* 💸 Priced **about the same to purchase**, yet **cheaper to renew** if you choose ongoing support

---

### Is EFCore.BulkExtensions faster than `SaveChanges()`?

Yes — for **simple bulk operations**, EFCore.BulkExtensions is **significantly faster** than `SaveChanges()`.
By batching operations and reducing database round-trips, it can handle large inserts or updates **much more efficiently** than EF Core’s default behavior.

However, there are two important exceptions to keep in mind:

* When processing a **small number of entities**, `SaveChanges()` is actually **faster**, since the setup overhead of bulk operations outweighs the benefits.
* When using **`IncludeGraph`** with **around 10,000 entities or more**, performance **drops sharply** — in our tests, it was **over 10x slower** than `SaveChanges()`. This issue only appears with **very large graphs**.

In short, EFCore.BulkExtensions is a great choice for **flat, large-scale bulk operations**, but it’s not recommended for **small batches** or **complex graphs involving many related entities**.

---

### Which EF Core versions are supported?

EFCore.BulkExtensions currently supports **EF Core 8** and **EF Core 9**.

All previous versions — from **EF Core 2 to 7** — are **no longer supported or maintained**.

At the moment, there’s also **no clear roadmap** or communication about how long support will continue for the latest EF Core versions, leaving long-term compatibility uncertain.

---

### Does EFCore.BulkExtensions support all databases?

EFCore.BulkExtensions **supports multiple databases**, including:

* SQL Server
* PostgreSQL
* MySQL
* Oracle
* SQLite

However, **some features only work reliably with SQL Server**.

Other providers often have **limited functionality, missing features, or compatibility issues**, especially with advanced options.

---

### Is EFCore.BulkExtensions still maintained?

No — **the library is effectively unmaintained** and has shown **no real activity for months**.

Aside from an occasional update to the **README file**, there’s been **no code changes, fixes, or responses to issues**.

This makes it **risky to rely on for production projects**, especially since open issues remain unanswered for long periods.

You can see this for yourself by visiting the [recent issues](https://github.com/borisdj/EFCore.BulkExtensions/issues) page on GitHub.

---

## 🧭 Summary

EFCore.BulkExtensions played an important role in bringing **free bulk operations** to the EF Core ecosystem — but its time has passed.

With a **restricted commercial license** and **no maintenance**, it’s now **hard to recommend** the official package for modern development.

**In short:**

* 🧩 Choose the [**MIT fork**](https://github.com/videokojot/EFCore.BulkExtensions.MIT) for a **free, open-source** alternative that remains community-driven.
* 🚀 Choose [**Entity Framework Extensions**](https://entityframework-extensions.net/) for **professional-grade performance, more features, and long-term support**.

No matter your choice, the **official EFCore.BulkExtensions package is best avoided** in new or production projects.