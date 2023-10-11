---
id: 5d06b58c-9ab4-4d57-83fd-793a11d57de4
position: 3
title: Is there any performance issue related to TPT inheritance in EF Core?
---

The `TPT` inheritance is often considered slower than [TPC](https://www.learnentityframeworkcore.com/inheritance/table-per-concrete) and [TPH](https://www.learnentityframeworkcore.com/inheritance/table-per-hierarchy).

You can see a benchmark made by Microsoft [here](https://learn.microsoft.com/en-us/ef/core/performance/modeling-for-performance#inheritance-mapping) 

Benchmark summary:

| Method | Mean | Allocated |
| ------ | ---- | --------- |
| TPC | 158.2 ms | 46 MB |
| TPH | 148.0 ms | 40 MB |
| TPT | 312.9 ms | 75 MB |

The TPT inheritance is the slowest by far and the one that requires the most memory.

This benchmark doesn't mean that a `TPT` inheritance is bad. In most situations, you will not see any performance difference unless you really need a highly scalable solution.
