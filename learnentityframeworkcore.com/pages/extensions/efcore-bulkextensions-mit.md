---
title: EFCore.BulkExtensions.MIT
description: An introduction to EFCore.BulkExtensions.MIT
canonical: /extensions/efcore-bulkextensions-mit
status: Published
lastmod: 2025-10-21
---

# EFCore.BulkExtensions.MIT

> **TL;DR:** [**EFCore.BulkExtensions.MIT**](https://github.com/videokojot/EFCore.BulkExtensions.MIT) is the recommended fork of the original [**EFCore.BulkExtensions**](https://github.com/borisdj/EFCore.BulkExtensions).
> The original library is no longer fully MIT licensed — it now uses a **dual license**, free for individuals and **paid for companies** with over **$1M annual revenue**.

The [**EFCore.BulkExtensions.MIT**](https://github.com/videokojot/EFCore.BulkExtensions.MIT) fork is the best choice if you still want a truly **free and open-source** library under the permissive **MIT license**.

There are several [reasons to use this fork instead of the original version](https://github.com/videokojot/EFCore.BulkExtensions.MIT?tab=readme-ov-file#why-choose-this-fork-instead-of-original-version):

* ✅ **MIT license** — completely free for personal and commercial use
* 🔧 **Additional bug fixes** not yet merged into the official repository
* ⚙️ **Supports more EF Core versions** than the main branch

If you’re looking for a **free, community-driven** solution for bulk operations, this fork is your best option.

If you need **professional performance, support, and frequent updates**, check out [**Entity Framework Extensions**](https://entityframework-extensions.net/) — a commercial library built and maintained by the same company since 2014.

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

* ⚡ **Faster than `SaveChanges()`** for simple and flat operations
* 🧩 **Easy to integrate** — works seamlessly with existing EF Core entities and configurations
* 🗄️ **Database agnostic** — compatible with multiple database providers
* 💡 **Completely free** for both personal and commercial projects

### ❌ Cons

* 🐢 **Performance drops sharply** — when handling a small number of entities or using `IncludeGraph` with large data sets
* 💤 **Not actively maintained** — very low GitHub activity and minimal responses to issues

---

## 🧪 Benchmarks – EFCore.BulkExtensions.MIT vs EF Core

The benchmark results are clear and easy to understand:

* ✅ **Significantly faster than `SaveChanges()`** for simple **Insert**, **Update**, or **InsertOrUpdate** operations when working with **large datasets**
* ⚠️ **Slightly slower than `SaveChanges()`** for a **small number of entities**, due to a fixed initialization cost
* ❌ **Up to 10x slower than `SaveChanges()`** when using **`IncludeGraph`** with **10,000+ entities**, making it **impractical for high-performance scenarios**

That last point is crucial — while the **`IncludeGraph`** feature works fine for smaller graphs, it experiences a dramatic performance drop with **large datasets** because of heavy memory usage.

You can view or reproduce these benchmark results directly in our [GitHub repository](https://github.com/zzzprojects/learnentityframeworkcore/tree/main/benchmarks/EFCore.BulkExtensions.MIT).

### Bulk Insert

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk Insert](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert.png)

### Bulk Insert (with Graph)

> This is not an error — EFCore.BulkExtensions.MIT `BulkInsert` was indeed more than **10x slower than `SaveChanges()`** when processing **100k entities**.

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk Insert with Graph](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-with-graph.png)

### Bulk Update

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk Update](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-update.png)

### Bulk InsertOrUpdate

![Benchmark EFCore vs EFCore.BulkExtensions.MIT – SQL Server - Bulk InsertOrUpdate](https://raw.githubusercontent.com/zzzprojects/learnentityframeworkcore/main/benchmarks/EFCore.BulkExtensions.MIT/benchmark-result/bulk-insert-or-update.png)

---

## ❓ FAQ

### Is EFCore.BulkExtensions.MIT free to use commercially?

Yes, you can use `EFCore.BulkExtensions.MIT` **completely free**, even in **commercial projects**.

Just be careful **not to confuse it** with the original [EFCore.BulkExtensions](https://github.com/borisdj/EFCore.BulkExtensions) package, which now follows a **dual license** — free for personal or open-source use, but **requires a paid license** for commercial projects.

---

### What’s the difference between EFCore.BulkExtensions.MIT and Entity Framework Extensions?

The main advantage of `EFCore.BulkExtensions.MIT` is that it’s **free**.
However, like most free libraries, it can’t match the same level of **features**, **performance**, or **support** that a professional library provides.

[**Entity Framework Extensions**](https://entityframework-extensions.net/) is a **paid library** created for developers who need more **power**, **reliability**, and **long-term support**. It includes:

* ⚙️ **More features**, **customization options**, and **integrations**
* 🧩 **Active support** and **frequent updates**
* ⚡ **Higher performance** in all use cases
* 🏆 **Professional maintenance** for stability and trust

If you just need a **free solution** for **simple bulk operations**, `EFCore.BulkExtensions.MIT` remains a **solid and dependable choice**.

---

### Is EFCore.BulkExtensions.MIT faster than `SaveChanges()`?

Yes — for **basic bulk operations**, EFCore.BulkExtensions.MIT is usually **much faster** than `SaveChanges()`.

By grouping operations together and reducing database round-trips, it can process large inserts or updates **far more efficiently** than EF Core’s default approach.

However, there are two key situations where performance drops:

* When working with a **small number of entities**, `SaveChanges()` is often **faster**, since the initialization cost of bulk operations outweighs their benefits.
* When using **`IncludeGraph`** with **around 10,000 entities or more**, performance **decreases significantly** — in our benchmarks, it was **more than 10x slower** than `SaveChanges()`. This slowdown only happens with **very large graphs**.

In summary, EFCore.BulkExtensions.MIT is excellent for **large, flat datasets**, but not ideal for **small batches** or **complex relational graphs**.

---

### Which EF Core versions are supported?

`EFCore.BulkExtensions.MIT` currently supports **EF Core 6**, **EF Core 7**, **EF Core 8**, and **EF Core 9**.

Make sure to use the **latest package version** to ensure full compatibility and best performance with your EF Core setup.

---

### Does EFCore.BulkExtensions.MIT support all databases?

`EFCore.BulkExtensions.MIT` **works with several databases**, including:

* SQL Server
* PostgreSQL
* MySQL
* Oracle
* SQLite

However, **full support is mainly available for SQL Server**.

Other providers may have **limited support**, **missing features**, or **occasional compatibility issues**, especially when using advanced options.

---

### 🧰 Is EFCore.BulkExtensions.MIT still maintained?

**No, not actively.**

The project is now mostly **community-driven**, but **almost all issues remain unanswered**.
There’s **no active maintainer**, and if you encounter a problem — especially in a **production environment** — you’ll most likely **not receive any help or fix**.

You can confirm this by checking the [recent issues](https://github.com/videokojot/EFCore.BulkExtensions.MIT/issues) page on GitHub.

---

## 🧭 Summary

`EFCore.BulkExtensions.MIT` is the **best free alternative** to the original `EFCore.BulkExtensions` library, offering **bulk insert, update, delete, and merge** operations under a **true MIT license**.

It’s a **great choice** for developers who need simple and efficient bulk operations without any licensing restrictions.

However, it’s important to understand its **limitations**:

* ⚙️ Works best for **large, flat datasets**
* 🐢 Slower for **small batches** or when using **IncludeGraph** on large graphs
* 💬 **Minimal maintenance** — limited community activity and slow responses on GitHub
* 💾 **Strong SQL Server support**, but **not all features are fully compatible** with other databases

For personal or small projects, this fork provides **excellent value**.

For professional, large-scale, or long-term solutions, [**Entity Framework Extensions**](https://entityframework-extensions.net/) remains the **most reliable and fully supported** option.
