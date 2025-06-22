---
Name: Soft Delete
---

# Soft Delete

## Description

Soft Delete is a mechanism in which a record or many records are marked in a database for deletion or to temporarily prevent them from being selected.

Instead of actually deleting a record in your database, you would just flag it as **IsDeleted = true**, and upon recovery of the record, you could just flag it as **IsDeleted = false**.


```csharp

modelBuilder.Filter("IsDeleted", (ISoftDelete d) => d.IsDeleted, false);

```

This is an example of a soft delete which will automatically filter those entities by applying the condition **IsDeleted==false**.
