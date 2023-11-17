---
id: dd15367b-c81a-4dc2-baad-a2446e43db31
position: 1
title: What is my personal (Jonathan Magnan) preference?
---

I prefer Dapper when reading data and [Dapper Plus](https://dapper-plus.net/) when saving data over EF Core. The combination of both libraries makes it perfect and gives me the best flexibility and performance.

Dapper gives me 100% control over how I select my data. It often happens that some `SELECT` are complex with multiples join, group by, where clause, so writing my own SQL makes perfect sense to ensure the statement is optimized. When I select data, I can choose all information in one database roundtrip and make sure only the information I need is selected.

Dapper Plus ensures that my data are inserted, updated, or deleted the fastest way possible and doesn't force me to create those SQL statements. Saving data is usually very simple, so I don't want to bother writing those statements for every entity type I could have to save. In case I need to customize my savings, Dapper Plus gives me all the options I need without worrying about writing my SQL.

It's a little bit contradicting as I want 100% control of the SQL when reading data and no control when saving, but at the same time:
- When reading data, 95% of my code requires writing custom SQL
- When saving data, 95% of my code doesn't require any custom SQL

Sure Dapper Plus is not free, nor is my time for writing and testing all SQL that will save data in the Database. 

Another important note:

- I mostly create personal projects such as [ZZZ Code AI](https://zzzcode.ai/)
- I don't have to handle a team. Only a few developers touch with me these projects