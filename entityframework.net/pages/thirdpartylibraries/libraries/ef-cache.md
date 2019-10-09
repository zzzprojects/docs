---
PermaID: 1000163
Name: EF Cache
---

# Cache

## Definition

**EntityFramework.Cache** provides a second level cache for Entity Framework 6.1 and newer. 

 - Entity Framework does not currently support caching of query results. 
 - A sample EF Caching provider is available for Entity Framework version 5 and earlier but due to changes to the provider model this sample provider does not work with Entity Framework 6 and newer. 

This library is filling the gap by enabling caching of query results for Entity Framework 6.1 and newer applications.

## Configuration

To use EntityFramework.Cache in your application, you need first configure EF using code-based configuration. 


```csharp
public class Configuration : DbConfiguration
{
  public Configuration()
  {
    var transactionHandler = new CacheTransactionHandler(new InMemoryCache());

    AddInterceptor(transactionHandler);

    var cachingPolicy = new CachingPolicy();

    Loaded +=
      (sender, args) => args.ReplaceService<DbProviderServices>(
        (s, _) => new CachingProviderServices(s, transactionHandler, 
          cachingPolicy));
  }
}
```

In the latest version, you can also use the new static EntityFrameworkCache.Initialize() method to configure EF to use EFCache. The Initialize method should be invoked before EF is used. 

For example, to initialize EFCache with the built-in InMemoryCache you can use the following code:


```csharp
EntityFrameworkCache.Initialize(new InMemoryCache());
```

## Requirements

### Entity Framework Version

 - Entity Framework 6.1 or later.

[Learn more](https://github.com/moozzyk/EFCache)