---
id: 46960478-1770-4d62-be96-6865864b9cc4
position: 3
title: Which ORM is the most performant between Dapper and EF Core?
---

Dapper is generally more performant than Entity Framework Core. As a micro-ORM, Dapper has less overhead and can quickly execute database operations. However, the difference in performance may not be significant for small to medium-sized applications or in scenarios where advanced features of EF Core are not heavily used. As always, the best choice depends on the specific requirements and constraints of your project.

Even if Dapper is the most performant, you will not notice the difference in most cases.