---
id: cf860f19-7c5f-48ed-af46-89c3ae7e6c79
position: 7
title: Can I combine TPH inheritance with another inheritance in EF Core?
---

No, you can only have one inheritance at a time. You cannot combine Table Per Hierarchy (TPC) with a [TPC](https://www.learnentityframeworkcore.com/inheritance/table-per-concrete) or [TPT](https://www.learnentityframeworkcore.com/inheritance/table-per-type) inheritance.