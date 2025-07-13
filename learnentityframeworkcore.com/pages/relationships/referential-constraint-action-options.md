---
title: Referential Constraint Action Options
description: Referential Constraint Actions are the actions that the database takes if an attempt is made to delete or update a primary key currently being referenced by a foreign key in another table. 
canonical: /relationships/referential-constraint-action-options
status: Published
lastmod: 2025-07-13
---

# EF Core Referential Constraint Action Options

To maintain the referential integrity of your data, you can specify the action that the database should take if an attempt is made to delete or update a primary key currently being referenced by a foreign key in another table. You can do this via the `ON DELETE` or `ON UPDATE` clause when the foreign key constraint is being defined.

Here's a diagram that shows a simple database of books and authors to help illustrate each option.

![Referential Integrity Constraints](/images/17-08-2016-14-01-13.png)

Authors are linked to books through the `AuthorId` foreign key on the Books table. The SQL for creating the foreign key constraint is as follows:

```sql
CONSTRAINT [FK_Books_Authors_AuthorId] FOREIGN KEY ([AuthorId]) REFERENCES [Authors] ([AuthorId])
```

The `ON DELETE` or `ON UPDATE` clause is usually appended to this.


### On Delete/Update No Action

This is the default option if `ON DELETE` or `ON UPDATE` is not specified. If an attempt is made to delete an Author record that is currently referenced by a Book record, the database will raise an error and the delete will be rolled back. The same will happen if an attempt is made to alter the primary key value of an Author record that is being used as a foreign key in a Book record.

### On Delete/Update Restrict

For the majority of relational database systems, `RESTRICT` is equivalent to `NO ACTION`. Where `RESTRICT` is specifically implemented by a database system, the difference is that `RESTRICT` results in the constraint being checked at the start of processing instead of at the end, which is the case with `NO ACTION` 

### On Delete/Update Set Null

If an Author record that is currently referenced in one or more Book records is deleted, the `AuthorId` value in the affected Book records will be updated to `NULL`. The same will happen if the Author record's primary key value is changed. This operation will only be successful if the `AuthorId` column in the Books table is nullable.

> **Note** This behavior is not enabled by default. You can use the Fluent API to opt into it by configuration.

### On Delete/Update Cascade

Under this option, when an Author is deleted, the operation will also cascade to dependent tables, resulting in all related books being deleted. When you update the primary key value for an author, all book records containing that value as a foreign key will be updated to the new foreign key value.


### On Delete/Update Set Default

When an Author record is deleted or has its key value amended, foreign key values in the Books table linked to the affected Author record will be changed to their default value. The foreign key fields in the affected tables must either have a default value specified, or be nullable, in which case `NULL` is considered the default value. Any non-NULL default value must relate to an existing key in the principal table.

