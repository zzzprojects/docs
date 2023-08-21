---
id: 7971ae5a-6f3a-43f4-af80-d3f6dd17baed
position: 1
title: What is the discriminator column when using a TPH inheritance in EF Core?
---

The discriminator column in the `TPH` inheritance is a special column added to the table to distinguish between different entity types.

Since a `TPH` inheritance stores all entity types in the same table, EF Core uses this column to identify which row corresponds to which entity type.