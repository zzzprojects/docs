---
Name: Key
---

# Key

## AllowDuplicateKeys
Gets or sets if a duplicate key is possible in the source.


```csharp
context.BulkMerge(options => options.AllowDuplicateKeys = true);
```

---

## AllowUpdatePrimaryKeys
Gets or sets if the key must also be included in columns to `UPDATE`.


```csharp
context.BulkMerge(options => options.AllowUpdatePrimaryKeys = true);
```
