---
title: MicroOrm.Dapper.Repositories
description: An introduction to MicroOrm.Dapper.Repositories
canonical: /extensions/microorm-dapper-repositories
status: Published
lastmod: 2025-04-21
---

# MicroOrm.Dapper.Repositories

[![ci](https://img.shields.io/github/actions/workflow/status/phnx47/dapper-repositories/ci.yml?branch=main&label=ci&logo=github%20actions&logoColor=white&style=flat-square)](https://github.com/phnx47/dapper-repositories/actions/workflows/ci.yml)
[![nuget](https://img.shields.io/nuget/v/MicroOrm.Dapper.Repositories?logo=nuget&style=flat-square)](https://www.nuget.org/packages/MicroOrm.Dapper.Repositories)
[![nuget](https://img.shields.io/nuget/dt/MicroOrm.Dapper.Repositories?logo=nuget&style=flat-square)](https://www.nuget.org/packages/MicroOrm.Dapper.Repositories)
[![codecov](https://img.shields.io/codecov/c/github/phnx47/dapper-repositories?logo=codecov&style=flat-square&token=wR4U6i4vhk)](https://codecov.io/gh/phnx47/dapper-repositories)
[![license](https://img.shields.io/github/license/phnx47/dapper-repositories?style=flat-square)](https://github.com/phnx47/dapper-repositories/blob/main/LICENSE)

## Description

If you like your code to run fast, you probably know about Micro ORMs. They are simple and one of their main goals is to be the fastest execution of your SQL sentences in you data
repository. For some Micro ORM's you need to write your own SQL sentences and this is the case of the most popular Micro
ORM [Dapper](https://github.com/DapperLib/Dapper)

This tool abstracts the generation of the SQL sentence for CRUD operations based on each C# POCO class "metadata". We know there are plugins for both Micro ORMs that implement the
execution of these tasks, but that's exactly where this tool is different. The "SQL Generator" is a generic component that generates all the CRUD sentences for a POCO class based
on its definition and the possibility to override the SQL generator and the way it builds each sentence.

The original idea was taken from [Diego García](https://github.com/ElNinjaGaiden/Dapper.DataRepositories).

## Installation

```sh
dotnet add package MicroOrm.Dapper.Repositories
```

## Docs

### Metadata attributes

**[Key]**
From `System.ComponentModel.DataAnnotations` - Use for primary key.

**[Identity]**
Property with an automatically incrementing identification number.

**[Table]**
From `System.ComponentModel.DataAnnotations.Schema` - By default the database table name will match the model name but it can be overridden with this.

**[Column]**
From `System.ComponentModel.DataAnnotations.Schema` - By default the column name will match the property name but it can be overridden with this.

**[NotMapped]**
From `System.ComponentModel.DataAnnotations.Schema` - For "logical" properties that do not have a corresponding column and have to be ignored by the SQL Generator.

**[Deleted], [Status]**
For tables that implement "logical deletes" instead of physical deletes. Use this to decorate the `bool` or `enum`.

**[LeftJoin]**
Left join for property: Collection and object are supported.

**[InnerJoin]**
Inner join for property: Collection and object are supported.

**[RightJoin]**
Right join for property: Collection and object are supported.

**[CrossJoin] - SQLite only**
Cross join for property: Collection and object are supported.

**[UpdatedAt]**
Automatically set DataTime.UtcNow (You can use local date or define offset) for Insert and Update.

### Notes

- By default the SQL Generator is going to map the POCO name with the table name, and each public property to a column.
- If the [Deleted] is used on a certain POCO, the sentence will be an update instead of a delete.
- Supports complex primary keys.
- Supports simple Joins.
- For this moment, with MSSQL you can only use limit with offset if you call OrderBy first, otherwise limit will be ignored.
- It has a problem when try to use GUID with dapper in Oracle. In this case it doesn't work. Look details: [Dapper#633](https://github.com/DapperLib/Dapper/issues/633), [Dapper#637](https://github.com/DapperLib/Dapper/issues/637)

### Maps

"Users" POCO:

```c#
[Table("Users")]
public class User
{
    [Key, Identity]
    public int Id { get; set; }

    public string ReadOnly => "test"; // because don't have set

    public string Name { get; set; }

    public int AddressId { get; set; }

    [LeftJoin("Cars", "Id", "UserId")]
    public List<Car> Cars { get; set; }

    [LeftJoin("Addresses", "AddressId", "Id")]
    public Address Address { get; set; }

    [Status, Deleted]
    public bool Deleted { get; set; }

    [UpdatedAt]
    public DateTime? UpdatedAt { get; set; }
}
```

"Cars" POCO:

```c#
[Table("Cars")]
public class Car
{
    [Key, Identity]
    public int Id { get; set; }

    public string Name { get; set; }

    public byte[] Data { get; set; }

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

Example Implements the simple repository:

```c#
public class UserRepository : DapperRepository<User>
{
    public UserRepository(IDbConnection connection, ISqlGenerator<User> sqlGenerator)
        : base(connection, sqlGenerator)
    {

    }
}
```

### Queries

Find by ID:

```c#
var user = await userRepository.FindAsync(x => x.Id == 5);
```

Query with limit:

```c#
var limit = 10u;
var users = await userRepository.SetLimit(limit).FindAllAsync();
```

Query with limit and offset:

```c#
var limit = 10u;
var offset = 5u;
var users = await userRepository.SetLimit(limit, offset).FindAllAsync();
```

Query with OrderBy:

```c#
var users = await userRepository.SetOrderBy(OrderInfo.SortDirection.DESC, x => x.CreatedAt).FindAllAsync();
```

Query with SetSelect:

```c#
var users = await userRepository.SetSelect(x => new {x.Id, x.Name}).FindAllAsync();
```

## Sponsors

[Dapper Plus](https://dapper-plus.net/) and [Entity Framework Extensions](https://entityframework-extensions.net/) are major sponsors and are proud to contribute to the development of MicroOrm.Dapper.Repositories

[![Dapper Plus](https://raw.githubusercontent.com/phnx47/dapper-repositories/main/dapper-plus-sponsor.png)](https://dapper-plus.net/bulk-insert)

[![Entity Framework Extensions](https://raw.githubusercontent.com/phnx47/dapper-repositories/main/entity-framework-extensions-sponsor.png)](https://entityframework-extensions.net/bulk-insert)

## License

All contents of this package are licensed under the [MIT license](https://opensource.org/licenses/MIT).