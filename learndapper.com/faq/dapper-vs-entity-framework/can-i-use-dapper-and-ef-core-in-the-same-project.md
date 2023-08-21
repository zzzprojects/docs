---
id: 2ed2ecaa-10be-4892-89c4-a37d4cf77649
position: 2
title: Can I use Dapper and EF Core in the same project?
---

Yes, you can use Dapper and EF Core in the same project but keep in mind that EF Core is complex, and Dapper is very simple to use, which makes a huge difference:

- If you already use EF Core, you can easily introduce Dapper to map results to entities for some special scenarios.
- If you already use Dapper, you should not introduce EF Core as it will make the project more complex.