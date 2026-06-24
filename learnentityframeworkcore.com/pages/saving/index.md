---
title: EF Core Saving Data
description: Learn how saving data works in Entity Framework Core, including SaveChanges, adding data, updating data, deleting data, connected and disconnected entities, direct database operations, transactions, and concurrency.
canonical: /saving
status: Published
lastmod: 2026-06-24
---

# Saving Data in EF Core

Saving data in EF Core usually starts with tracked entity changes and `SaveChangesAsync()`, but some scenarios require direct database operations, raw SQL, transactions, or concurrency handling.

This page helps you choose the right saving article for your scenario.

## Start with SaveChanges

`SaveChangesAsync()` is the standard way to persist tracked entity changes in EF Core.

The normal workflow is to create or load entities, change them, and call `SaveChangesAsync()`.

In this workflow:

* EF Core tracks entity state in the current `DbContext`.
* The application creates, modifies, or removes entities in memory.
* `SaveChangesAsync()` sends the required `INSERT`, `UPDATE`, or `DELETE` commands to the database.

For the complete overview, see [SaveChanges](/saving/save-changes).

## Choose an EF Core Saving Workflow

Use the articles in this section based on what you need to do.

### Save tracked entity changes

Use these articles when your workflow is based on entity instances tracked by a `DbContext`.

* [SaveChanges](/saving/save-changes) — persist tracked changes to the database
* [Adding Data](/saving/adding-data) — insert new entities
* [Updating Data](/saving/modifying-data) — update existing entities
* [Deleting Data](/saving/deleting-data) — delete entities

### Understand connected and disconnected saving

Use these articles when the important question is whether EF Core is already tracking the entity.

* [Connected Entities](/saving/save-changes-connected-entities) — save entities that are already tracked by the current `DbContext`
* [Disconnected Entities](/saving/save-changes-disconnected-entities) — save entities that are not tracked by the current `DbContext`

### Understand how tracking affects saving

Use these articles when you need to understand how EF Core knows what changed.

* [Change Tracker](/saving/change-tracker) — understand the `ChangeTracker` component and entity states
* [How Change Tracker Works](/saving/change-tracker-how-it-works) — understand how EF Core tracks changes and decides what should be saved

### Delete related data safely

Use this article when deleting one entity may affect related entities.

* [Cascade Delete](/saving/cascade-delete) — understand what happens to related data when an entity is deleted

### Execute changes directly in the database

Use these articles when you do not want the normal `ChangeTracker` + `SaveChangesAsync()` workflow.

* [ExecuteUpdate](/saving/execute-update) — update rows directly in the database without loading entities
* [ExecuteDelete](/saving/execute-delete) — delete rows directly in the database without loading entities
* [ExecuteSql](/saving/execute-sql) — run raw SQL commands that modify data

### Control transactions and concurrency

Use these articles when the saving workflow involves multiple operations, rollback, or conflicting changes from multiple users or processes.

* [Transactions](/saving/transactions) — use default transactions, explicit transactions, savepoints, shared transactions, and `TransactionScope`
* [Concurrency](/saving/concurrency) — understand what happens when multiple users or processes modify the same data
* [Database Wins](/saving/concurrency-database-wins) — resolve a concurrency conflict by keeping the database values
* [Client Wins](/saving/concurrency-client-wins) — resolve a concurrency conflict by keeping the current application values
* [Custom Resolution](/saving/concurrency-custom-resolution) — resolve a concurrency conflict by choosing values property by property

## The Main Saving Decision: Tracked Workflow or Direct Execution

Most EF Core saving decisions come down to one question: should EF Core save tracked entity instances, or should the operation execute directly in the database?

Use `SaveChangesAsync()` when your workflow is based on entity instances and the `ChangeTracker`.

This is the normal choice when the application creates, modifies, or removes entities and then asks EF Core to persist those tracked changes.

Use direct execution methods when the operation should run directly in the database without loading or tracking entities.

A simple rule is:

* use `SaveChangesAsync()` when the workflow is based on entity instances and tracking
* use `ExecuteUpdateAsync()` when matching rows can be updated with one database-side rule
* use `ExecuteDeleteAsync()` when matching rows can be deleted with one database-side rule
* use `ExecuteSqlAsync()` when you need to write and execute the SQL command yourself

These direct execution methods bypass the normal `ChangeTracker` + `SaveChangesAsync()` workflow.

That makes them useful for set-based operations, cleanup jobs, direct SQL commands, or cases where loading entity instances would add unnecessary overhead.

They are usually not the best choice when you need tracked entity behavior, in-memory business rules, or already tracked entity instances to stay synchronized automatically.

## Tracking Determines What SaveChanges Saves

`SaveChangesAsync()` only persists changes that EF Core knows about through the current `DbContext`.

That is why tracking is one of the most important concepts in the saving workflow.

If an entity is already tracked, EF Core can usually detect property changes and save them naturally.

If an entity is not tracked, the application must tell EF Core what should happen to it, such as whether it should be added, updated, attached, or removed.

This distinction is the difference between connected and disconnected saving:

* in a connected workflow, the same `DbContext` usually loads, tracks, modifies, and saves the entity
* in a disconnected workflow, the saving `DbContext` is not already tracking the entity

For connected workflows, see [Connected Entities](/saving/save-changes-connected-entities).

For disconnected workflows, see [Disconnected Entities](/saving/save-changes-disconnected-entities).

For the tracking component itself, see [Change Tracker](/saving/change-tracker).

For a deeper explanation of how EF Core tracks changes and decides what was modified, see [How Change Tracker Works](/saving/change-tracker-how-it-works).

## Related Data Affects Delete Behavior

After you understand basic deletes, the next question is what happens to related rows.

Deleting one entity can affect related entities depending on the relationship configuration and the selected `DeleteBehavior`.

For example, deleting a principal entity can:

* delete dependent entities
* set foreign key values to `null`
* fail because related data still exists
* take no automatic action

Use [Cascade Delete](/saving/cascade-delete) when you need to understand how related data is affected by delete operations.

## Transactions and Concurrency

After you choose how data should be saved, two advanced questions often appear:

* should several operations succeed or fail together?
* what should happen if another user or process changes the same data first?

Use [Transactions](/saving/transactions) when several operations must be treated as one unit of work.

Use [Concurrency](/saving/concurrency) when the application must detect and resolve conflicting changes.

### Transactions

For most relational database providers, EF Core automatically uses a transaction for a single `SaveChangesAsync()` call.

Use explicit transactions when several operations must succeed or fail together, such as:

* multiple `SaveChangesAsync()` calls
* several raw SQL commands
* a mix of tracked changes and direct database operations
* a workflow that must be rolled back if one step fails

For more details, see [Transactions](/saving/transactions).

### Concurrency

Concurrency conflicts can happen when two users or processes load the same row, change it, and try to save different values.

Start with [Concurrency](/saving/concurrency) to understand optimistic concurrency, conflict detection, and `DbUpdateConcurrencyException`.

Then use the resolution strategy that matches the application rule:

* [Database Wins](/saving/concurrency-database-wins) — keep the latest database values
* [Client Wins](/saving/concurrency-client-wins) — keep the current application values
* [Custom Resolution](/saving/concurrency-custom-resolution) — choose values property by property

## Summary

Saving data in EF Core depends on the workflow:

* `SaveChangesAsync()` is the standard API for saving tracked entity changes
* `Add()`, `Update()`, `Remove()`, and range methods cover the normal entity-based insert, update, and delete workflow
* connected and disconnected scenarios depend on whether the saving `DbContext` is already tracking the entity
* the `ChangeTracker` stores the state information EF Core uses to decide what should be saved
* `ExecuteUpdateAsync()`, `ExecuteDeleteAsync()`, and `ExecuteSqlAsync()` execute directly in the database and bypass the normal tracked workflow
* transactions and concurrency handling are advanced saving concerns for multi-step operations and conflicting changes
