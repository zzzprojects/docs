---
Name: Out of Memory Troubleshooting
LastMod: 2023-02-26
---

# Out of Memory

## Problem

You execute a method from the Entity Framework Extensions library, and the following error is thrown:

> An exception of type `System.OutOfMemoryException` occurred in...

## Cause
That error is caused when the library consumes too much memory.

Most of the time, this error is caused by some methods used from `Entity Framework` for command generations that will be used later by our library.

### Solution
Turning off Entity Framework Propagation

This solution works with around 99% of models. By turning off this option, our library no longer uses several methods from Entity Framework which consume high memory, such as the command generation.

See: <a href="/improve-bulk-savechanges">Improve BulkSaveChanges</a>

### Bulk Operations
In some exceptional scenarios, a better option is using Bulk Operations such as:

- BulkInsert
- BulkUpdate
- BulkDelete
- BulkMerge

Bulk Operations are faster and consume way less memory than BulkSaveChanges.

### Reduce the number of entities in the ChangeTracker
Another way is to make sure the ChangeTracker doesn't contain too many entities.

The ChangeTracker has not been built to support millions of entities.
