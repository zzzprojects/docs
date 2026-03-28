---
title: MicroOrm.Dapper.Repositories
description: An introduction to MicroOrm.Dapper.Repositories
canonical: /extensions/microorm-dapper-repositories
status: Published
lastmod: 2026-03-28
---

# MicroOrm.Dapper.Repositories

[![ci](https://img.shields.io/github/actions/workflow/status/phnx47/dapper-repositories/ci.yml?branch=main&label=ci&logo=github%20actions&logoColor=white&style=flat-square)](https://github.com/phnx47/dapper-repositories/actions/workflows/ci.yml)
[![nuget](https://img.shields.io/nuget/v/MicroOrm.Dapper.Repositories?logo=nuget&style=flat-square)](https://www.nuget.org/packages/MicroOrm.Dapper.Repositories)
[![nuget](https://img.shields.io/nuget/dt/MicroOrm.Dapper.Repositories?logo=nuget&style=flat-square)](https://www.nuget.org/packages/MicroOrm.Dapper.Repositories)
[![codecov](https://img.shields.io/codecov/c/github/phnx47/dapper-repositories?logo=codecov&style=flat-square&token=wR4U6i4vhk)](https://codecov.io/gh/phnx47/dapper-repositories)
[![license](https://img.shields.io/github/license/phnx47/dapper-repositories?style=flat-square)](https://github.com/phnx47/dapper-repositories/blob/main/LICENSE)

## Description

If you like your code to run fast, you probably know about Micro ORMs. They are simple and one of their main goals is to be the fastest execution of your SQL sentences in your data
repository. For some Micro ORMs you need to write your own SQL sentences and this is the case of the most popular Micro
ORM [Dapper](https://github.com/DapperLib/Dapper).

This library abstracts the generation of SQL for CRUD operations based on each C# POCO class metadata. The SQL Generator is a generic component that builds all CRUD sentences for a POCO class based on its definition, with the possibility to override the SQL generator and the way it builds each sentence.

The library is designed to help with common, simple queries so you don't have to write repetitive SQL by hand. It does not try to cover every possible scenario - for complex queries you should write SQL directly using [Dapper](https://github.com/DapperLib/Dapper).

## Installation

```sh
dotnet add package MicroOrm.Dapper.Repositories
```

## Configuration

There are two ways to configure SQL generation:

**1. Global static configuration** - `MicroOrmConfig` sets defaults for all repositories at once. Useful when your application uses a single database provider.

```c#
MicroOrmConfig.SqlProvider = SqlProvider.PostgreSQL;
MicroOrmConfig.UseQuotationMarks = true;
MicroOrmConfig.TablePrefix = "app_";
MicroOrmConfig.AllowKeyAsIdentity = true;
```

**2. Per-repository configuration** - pass a `SqlGenerator<TEntity>` with specific settings to each repository constructor. Useful when you need different settings per entity or work with multiple providers.

```c#
var sqlGenerator = new SqlGenerator<User>(SqlProvider.PostgreSQL, useQuotationMarks: true);
var repo = new DapperRepository<User>(connection, sqlGenerator);
```

| Property | Default | Description |
|---|---|---|
| `SqlProvider` | `MSSQL` | SQL dialect to use |
| `UseQuotationMarks` | `false` | Quote table and column names in generated SQL |
| `TablePrefix` | `null` | String prepended to all table names (e.g. for multi-tenant scenarios) |
| `AllowKeyAsIdentity` | `false` | Treat `[Key]` as `[Identity]` when `[Identity]` is not explicitly set |

**Supported providers:** `MSSQL`, `MySQL`, `PostgreSQL`, `SQLite`, `Oracle`

## Entity Mapping

### Metadata Attributes

| Attribute | Description |
|---|---|
| `[Key]` | Primary key. Apply to multiple properties for composite keys |
| `[Identity]` | Auto-incrementing property. Excluded from INSERT, populated after insert |
| `[Table]` | Overrides table name. Supports `Schema` parameter |
| `[Column]` | Overrides column name. Supports `Order` parameter |
| `[NotMapped]` | Excludes property from SQL generation |
| `[IgnoreUpdate]` | Excludes property from UPDATE statements only |
| `[Deleted]` | Enables logical (soft) delete - DELETE becomes UPDATE. Works with `bool` or `enum` (apply to the enum member representing deleted state) |
| `[Status]` | Used together with `[Deleted]` on the status property |
| `[UpdatedAt]` | Auto-sets timestamp on Insert and Update. Defaults to `DateTime.UtcNow`. Properties: `TimeKind` (`Utc`, `Local`, `Unspecified`), `OffSet` (hours) |

### Join Attributes

All join attributes support both single-object and collection navigation properties. Constructor parameters: `(tableName, key, externalKey)` with optional `tableSchema` and `tableAlias`. `[CrossJoin]` takes only `(tableName)` since CROSS JOIN has no ON clause.

| Attribute | SQL |
|---|---|
| `[LeftJoin]` | LEFT JOIN |
| `[InnerJoin]` | INNER JOIN |
| `[RightJoin]` | RIGHT JOIN |
| `[CrossJoin]` | CROSS JOIN |

### Entity Examples

```c#
[Table("Users")]
public class User
{
    [Key, Identity]
    public int Id { get; set; }

    public string ReadOnly => "test"; // read-only properties are ignored

    public string Name { get; set; }

    public int AddressId { get; set; }

    [LeftJoin("Cars", "Id", "UserId")]
    public List<Car> Cars { get; set; }

    [LeftJoin("Addresses", "AddressId", "Id")]
    public Address Address { get; set; }

    [InnerJoin("Phones", "Id", "UserId")]
    public Phone Phone { get; set; }

    [Status, Deleted]
    public bool Deleted { get; set; }

    [UpdatedAt]
    public DateTime? UpdatedAt { get; set; }
}
```

Enum-based soft delete:

```c#
[Table("Cars")]
public class Car
{
    [Key, Identity]
    public int Id { get; set; }

    public string Name { get; set; }

    public int UserId { get; set; }

    [LeftJoin("Users", "UserId", "Id")]
    public User User { get; set; }

    [Status]
    public StatusCar Status { get; set; }
}

public enum StatusCar
{
    Inactive = 0,
    Active = 1,

    [Deleted]
    Deleted = -1
}
```

Composite primary key:

```c#
[Table("Reports")]
public class Report
{
    [Key]
    public int Id { get; set; }

    [Key]
    public int AnotherId { get; set; }
}
```

Table with schema:

```c#
[Table("Phones", Schema = "DAB")]
public class Phone
{
    [Key, Identity]
    public int Id { get; set; }

    [IgnoreUpdate]
    public string Code { get; set; }

    public string PNumber { get; set; }

    [Status, Deleted]
    public bool? IsDeleted { get; set; }
}
```

## Setting Up Repositories

### Using DbContext

Create a context class that inherits from `DapperDbContext` and exposes repository properties:

```c#
public class MyDbContext : DapperDbContext
{
    private IDapperRepository<User>? _users;
    private IDapperRepository<Car>? _cars;
    private IReadOnlyDapperRepository<Report>? _reports;

    public MyDbContext(IDbConnection connection) : base(connection) { }

    public IDapperRepository<User> Users =>
        _users ??= new DapperRepository<User>(Connection);

    public IDapperRepository<Car> Cars =>
        _cars ??= new DapperRepository<Car>(Connection);

    // Use IReadOnlyDapperRepository for read-only access
    public IReadOnlyDapperRepository<Report> Reports =>
        _reports ??= new ReadOnlyDapperRepository<Report>(Connection);
}
```

With a custom SQL generator (to specify provider or quotation marks per-repository):

```c#
public IDapperRepository<User> Users =>
    _users ??= new DapperRepository<User>(Connection, new SqlGenerator<User>(SqlProvider.PostgreSQL, true));
```

### Standalone Repository

```c#
var connection = new SqlConnection(connectionString);
var userRepo = new DapperRepository<User>(connection);
```

Or with a custom repository class:

```c#
public class UserRepository : DapperRepository<User>
{
    public UserRepository(IDbConnection connection, ISqlGenerator<User> sqlGenerator)
        : base(connection, sqlGenerator)
    {
    }
}
```

## CRUD Operations

All operations have both synchronous and async overloads. Async methods accept an optional `CancellationToken`. Most methods accept an optional `IDbTransaction`.

### Find

```c#
// Find by predicate (returns first match or null)
var user = await repo.FindAsync(x => x.Id == 5);

// Find without predicate (returns first record)
var first = await repo.FindAsync();

// FindById
var user = await repo.FindByIdAsync(5);
```

### FindAll

```c#
// All records
var users = await repo.FindAllAsync();

// With WHERE clause
var activeUsers = await repo.FindAllAsync(x => x.Name == "John");

// With multiple conditions
var users = await repo.FindAllAsync(x => x.AddressId == 1 && !x.Deleted);
```

**Supported LINQ expressions in predicates:**
- Equality: `x => x.Name == "John"`
- Comparison: `x => x.Id > 5`
- Contains (IN clause): `x => idList.Contains(x.Id)`
- String methods: `x => x.Name.StartsWith("J")`, `EndsWith()`, `Contains()`
- Boolean: `x => x.IsActive`, `x => !x.Deleted`
- Null checks: `x => x.Name != null`
- Logical operators: `&&`, `||`

### FindAllBetween

```c#
// Between with DateTime
var users = await repo.FindAllBetweenAsync(
    new DateTime(2023, 1, 1),
    new DateTime(2023, 12, 31),
    x => x.CreatedAt);

// Between with predicate
var users = await repo.FindAllBetweenAsync(
    new DateTime(2023, 1, 1),
    new DateTime(2023, 12, 31),
    x => x.CreatedAt,
    x => x.Name == "John");
```

### Count

```c#
// Count all
var total = await repo.CountAsync();

// Count with predicate
var count = await repo.CountAsync(x => x.Name == "John");

// Count with DISTINCT
var uniqueCount = await repo.CountAsync(x => x.AddressId);

// Count with DISTINCT and predicate
var count = await repo.CountAsync(x => x.IsActive, x => x.AddressId);
```

### Insert

```c#
var user = new User { Name = "John", AddressId = 1 };
bool success = await repo.InsertAsync(user);
// user.Id is populated after insert if [Identity] is used
```

### Bulk Insert

```c#
var users = new List<User>
{
    new() { Name = "John", AddressId = 1 },
    new() { Name = "Jane", AddressId = 2 }
};
int insertedCount = await repo.BulkInsertAsync(users);
```

### Update

```c#
var user = await repo.FindByIdAsync(1);
user.Name = "Updated Name";
bool success = await repo.UpdateAsync(user);
```

Update specific columns only (include list):

```c#
// Only update the Name column
bool success = await repo.UpdateAsync(user, x => x.Name);
```

Update with predicate (WHERE clause):

```c#
bool success = await repo.UpdateAsync(x => x.Id == 1, user);
```

### Bulk Update

```c#
var users = (await repo.FindAllAsync()).ToList();
foreach (var user in users)
    user.Name = "Updated";
bool success = await repo.BulkUpdateAsync(users);
```

### Delete

With entity instance:

```c#
var user = await repo.FindByIdAsync(1);
bool success = await repo.DeleteAsync(user);
```

With predicate:

```c#
bool success = await repo.DeleteAsync(x => x.Id == 1);
```

With timeout:

```c#
bool success = await repo.DeleteAsync(x => x.Id == 1, TimeSpan.FromSeconds(30));
```

> **Note:** If the entity has a `[Deleted]` attribute, delete operations generate an UPDATE statement (logical delete) instead of a physical DELETE.

## Joins

Load related entities by specifying navigation property expressions:

```c#
// Find with one join
var user = await repo.FindAsync<Car>(
    x => x.Id == 1,
    x => x.Cars);

// Find with multiple joins (up to 6 supported)
var user = await repo.FindAsync<Car, Address>(
    x => x.Id == 1,
    x => x.Cars,
    x => x.Address);

// FindAll with joins
var users = await repo.FindAllAsync<Car>(
    x => x.AddressId == 1,
    x => x.Cars);

// FindById with joins
var user = await repo.FindByIdAsync<Car, Address>(
    1,
    x => x.Cars,
    x => x.Address);
```

WHERE clauses can reference joined entity properties:

```c#
var users = await repo.FindAllAsync(x => x.Phone.PNumber == "123");
```

## Filtering and Pagination

`SetLimit`, `SetOrderBy`, `SetGroupBy`, and `SetSelect` return the repository instance for fluent chaining. By default, filter settings are cleared after each query. Use the `permanent` parameter to persist them across queries.

### Limit and Offset

```c#
// Limit
var users = await repo.SetLimit(10).FindAllAsync();

// Limit with offset (pagination)
var page2 = await repo.SetLimit(10, 10).FindAllAsync();

// Permanent limit (persists across queries)
repo.SetLimit(100, 0, permanent: true);
```

### OrderBy

```c#
// Order by expression
var users = await repo.SetOrderBy(OrderInfo.SortDirection.DESC, x => x.Name).FindAllAsync();

// Order by multiple columns
var users = await repo.SetOrderBy(OrderInfo.SortDirection.ASC, "Name", "Id").FindAllAsync();

// Order by joined table column
var users = await repo.SetOrderBy<Address>(OrderInfo.SortDirection.ASC, x => x.CityId).FindAllAsync();

// Custom order query
var users = await repo.SetOrderBy("Name ASC, Id DESC").FindAllAsync();

// Clear ordering
repo.SetOrderBy();
```

> **MSSQL note:** When using MSSQL, limit with offset requires an ORDER BY clause. Otherwise the limit is ignored.

### GroupBy

```c#
var users = await repo.SetGroupBy(x => x.AddressId).FindAllAsync();

// Permanent group by
repo.SetGroupBy(permanent: true, x => x.AddressId);
```

### Select (Column Projection)

```c#
// Select specific columns
var users = await repo.SetSelect(x => new { x.Id, x.Name }).FindAllAsync();

// Select from joined table
repo.SetSelect<Address>(x => new { x.CityId, x.Street });

// Custom column list
repo.SetSelect("Id", "Name");
```

## Transactions

Use `DapperDbContext` for transaction management:

```c#
using var context = new MyDbContext(connection);
var transaction = context.BeginTransaction();

try
{
    var user = new User { Name = "John", AddressId = 1 };
    await context.Users.InsertAsync(user, transaction);

    var car = new Car { Name = "Tesla", UserId = user.Id };
    await context.Cars.InsertAsync(car, transaction);

    transaction.Commit();
}
catch
{
    transaction.Rollback();
    throw;
}
```

Async transaction:

```c#
var transaction = await context.BeginTransactionAsync();
```

Transactions can also be passed directly to any repository method:

```c#
await repo.InsertAsync(entity, transaction);
await repo.UpdateAsync(entity, transaction);
await repo.DeleteAsync(entity, transaction);
await repo.FindAsync(x => x.Id == 1, transaction);
```

## Notes

- By default, the SQL Generator maps the POCO class name to the table name, and each public property with a getter and setter to a column.
- Read-only properties (expression-bodied or without a setter) are automatically ignored.
- If `[Deleted]` is used, DELETE operations generate UPDATE statements instead.
- Supports composite primary keys (multiple `[Key]` attributes).
- Supports up to 6 joined entities in a single query.
- Property metadata is cached using `ConcurrentDictionary` for performance.
- GUID primary keys do not work with Oracle due to Dapper limitations. See: [Dapper#633](https://github.com/DapperLib/Dapper/issues/633), [Dapper#637](https://github.com/DapperLib/Dapper/issues/637).

## Sponsors

[Dapper Plus](https://dapper-plus.net/?utm_source=phnx47&utm_medium=MicroOrm.Dapper.Repositories) and [Entity Framework Extensions](https://entityframework-extensions.net/?utm_source=phnx47&utm_medium=MicroOrm.Dapper.Repositories) are major sponsors and are proud to contribute to the development of [MicroOrm.Dapper.Repositories](https://github.com/phnx47/dapper-repositories)

[![Dapper Plus](https://raw.githubusercontent.com/phnx47/dapper-repositories/main/dapper-plus-sponsor.png)](https://dapper-plus.net/bulk-insert?utm_source=phnx47&utm_medium=MicroOrm.Dapper.Repositories)

[![Entity Framework Extensions](https://raw.githubusercontent.com/phnx47/dapper-repositories/main/entity-framework-extensions-sponsor.png)](https://entityframework-extensions.net/bulk-insert?utm_source=phnx47&utm_medium=MicroOrm.Dapper.Repositories)

## License

Inspired by [Diego García's Dapper.DataRepositories](https://github.com/ElNinjaGaiden/Dapper.DataRepositories).

All contents of this package are licensed under the [MIT license](https://opensource.org/licenses/MIT).
