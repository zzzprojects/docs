---
PermaID: 1000213
Name: Third Party Library
---

# Dapper - Third Party Library
Dapper Third Party Library allow you to extend dapper functionality which is not available in the core Dapper library, for example, basic CRUD operations, multi-mapping, manual mapping and convention based mapping, etc.

## Why Third Party Library?
Dapper is a King of Micro ORM in terms of performance, but a lot of essential features is missing for some application scenarios.

The only way to achieve is either create code for this kind of scenario or use a library which fully or partially cover them.

| Library | Type | Support | Documentation | Features |
| :------ | :---------- | :------: | :------------ | :------- |
| [Dapper Plus](dapper-plus-third-party-library) | PRO | < 1 Day | [Yes](http://dapper-plus.net/tutorials) | Bulk Insert<br>Bulk Delete<br>Bulk Update<br>Bulk Merge<br>Bulk Action Async<br>Bulk Also Action<br>Bulk Then Action |
| [Dapper Transaction](dapper-plus-third-party-library) | Free | < 1 Day | [Yes](https://github.com/zzzprojects/Dapper.Transaction) | `IDbTransaction` extension methods for Dapper: A high performance Micro-ORM supporting SQL Server, MySQL, Sqlite, SqlCE, Firebird etc..  |
| [Dapper-Async](dapper-async) | Free | 2 - 3 Days | No | ExecuteAsync<br>QueryAsync<br>QueryFirstAsync<br>QueryFirstOrDefaultAsync<br>QuerySingleAsync<br>QuerySingleOrDefaultAsync<br>QueryMultipleAsync |
| [Dapper.Contrib](dapper-contrib-third-party-library) | Free | 2 - 3 Days | [Yes](https://github.com/StackExchange/Dapper/tree/master/Dapper.Contrib) | Get<br>GetAll<br>Insert<br>Update<br>Delete<br>DeleteAll |
| [DapperExtensions](dapper-extensions) | Free | No | No | Get<br>GetList<br>Insert<br>Update<br>Delete<br>Count |
| [Dapper.FastCrud](dapper-fastcrud) | Free | < 1 Day | [Yes](https://github.com/MoonStorm/Dapper.FastCRUD/wiki) | Get<br>Find<br>Insert<br>Update<br>BulkUpdate<br>Delete<br>BulkDelete<br>Count |
| [Dapper.FluentMap](dapper-fluentmap) | Free | 2 - 5 Days | [Yes](https://github.com/henkmollema/Dapper-FluentMap) | Manual mapping<br>Convention based mapping |
| [Dapper.Mapper](dapper-mapper) | Free | No | No | Multi-mapping |
| [Dapper.Rainbow](dapper-rainbow) | Free | 2 - 3 Days | No | Get<br>Insert<br>Update<br>Delete<br>All |
| [Dapper.SimpleCRUD](dapper-simplecrud) | Free | 1 - 3 Days | [Yes](https://github.com/ericdc1/Dapper.SimpleCRUD) | Get<br>GetList<br>GetListPaged<br>Insert<br>Update<br>Delete<br>DeleteList<br>RecordCount |
| [Dapper.SimpleSave](dapper-simplesave) | Free | No | [Yes](https://github.com/Paymentsense/Dapper.SimpleSave/wiki/) | Create<br>CreateAll<br>Update<br>UpdateAll<br>Delete<br>DeleteAll<br>SoftDelete<br>SoftDeleteAll |