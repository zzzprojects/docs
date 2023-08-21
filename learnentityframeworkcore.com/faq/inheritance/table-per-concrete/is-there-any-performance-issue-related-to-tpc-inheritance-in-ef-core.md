---
id: a43deb94-325b-4519-829d-c02c2351f4a7
position: 3
title: Is there any performance issue related to TPC inheritance in EF Core?
---

The TPC inheritance could become slow if you have too many concrete types.

If you query the root entity, a SQL will be created with a `UNION ALL` join that will include all tables from your concrete class.

```sql
SELECT [ColumnList], 'DiscriminatorName1' AS [Discriminator]
FROM [TableName1]
WHERE [predicate]
UNION ALL
SELECT [ColumnList], 'DiscriminatorName2' AS [Discriminator]
FROM [TableName2]
WHERE [predicate]
UNION ALL
SELECT [ColumnList], 'DiscriminatorName3' AS [Discriminator]
FROM [TableName3]
WHERE [predicate]
UNION ALL
SELECT [ColumnList], 'DiscriminatorName4' AS [Discriminator]
FROM [TableName4]
WHERE [predicate]
```

There is no problem if you have only 2 or 3 tables, but if you implement a strategy with many concrete classes, you might end up with some performance issues when querying.

That is one of the reasons why this is recommended to only have 2 or 3 concrete classes in the TPC strategy.