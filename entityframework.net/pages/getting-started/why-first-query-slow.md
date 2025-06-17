---
PermaID: 1000066
Title: Entity Framework First Query Slow - Learn Why First Load Takes Time
MetaDescription: Unlock the power of Entity Framework by understanding why the first query is slow. Learn why the first load can take several seconds, why it's fast after, and how to cache and pre-compile views.
LastMod: 2025-06-17
Tags: getting-started performance
---

# Entity Framework First Query Slow: Discover Why First Load Takes Time

## Why Entity Framework First Load is Slow?

Entity Framework is very slow to load for the first time after every compilation especially when you have a large model.

### StackOverflow Related Questions

 - [Entity Framework 6.2 very slow first startup and EFInteractiveViews](https://stackoverflow.com/questions/52376465/entity-framework-6-2-very-slow-first-startup-and-efinteractiveviews/52405059#52405059)

## Answer

Entity Framework loads very slowly the first time because the first query causes EF compiles the model. If you are using EF 6.2, you can use a Model Cache which loads a prebuilt EDMX when using code first; instead, EF generates it on startup.

```csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration() : base()
    {
        var path = Path.GetDirectoryName(this.GetType().Assembly.Location);
        SetModelStore(new DefaultDbModelStore(path));
    }
}
```

To speed up your application startup time in an earlier version of EF, here are three suggestions mentioned in this post.

 - Using a Cached DbModelStore
 - Generate pre-compiled Views
 - Generate the pre-compiled version of Entity Framework using NGen to avoid jitting

### Using a Cached DbModelStore

It will cache the Code First pipeline mapping and store it in a XML file. The next time your application starts, EF will deserialize this cached mapping file which significantly reduces startup time. The cached DbModelStore can be enabled with the following lines of code.


```csharp
public class MyContextConfiguration : DbConfiguration
{
    public MyContextConfiguration()
    {
        MyDbModelStore cachedDbModelStore = new MyDbModelStore(MyContext.EfCacheDirPath);
        IDbDependencyResolver dependencyResolver = new SingletonDependencyResolver<DbModelStore>(cachedDbModelStore);
        AddDependencyResolver(dependencyResolver);
    }

    private class MyDbModelStore : DefaultDbModelStore
    {
        public MyDbModelStore(string location)
            : base(location)
        {}

        public override DbCompiledModel TryLoad(Type contextType)
        {
            string path = GetFilePath(contextType);
            if(File.Exists(path))
            {
                DateTime lastWriteTime = File.GetLastWriteTimeUtc(path);
                DateTime lastWriteTimeDomainAssembly = File.GetLastWriteTimeUtc(typeof(MyDomain.SomeTypeInOurDomain).Assembly.Location);
                if (lastWriteTimeDomainAssembly > lastWriteTime)
                {
                    File.Delete(path);
                    Tracers.EntityFramework.TraceInformation("Cached db model obsolete. Re-creating cached db model edmx.");
                }
            }
            else
            {
                Tracers.EntityFramework.TraceInformation("No cached db model found. Creating cached db model edmx.");
            }

            return base.TryLoad(contextType);
        }
    }
}
```

[Learn more about Cached DbModelStore](https://gist.github.com/davidroth/9886349)

### Generate Precompiled Views

View generation can have a significant enhancement on startup performance, so caching this step is a must to achieve good EF startup performance. There are different ways to generate and store pre-compiled views such as;

 - [Entity Framework Pre-Generated Mapping Views](https://msdn.microsoft.com/en-us/library/dn469601%28v=vs.113%29.aspx?f=255&MSPPError=-2147217396)
 - [EFInteractiveViews](https://github.com/moozzyk/EFInteractiveViews)

### Generate Pre-compiled Version of EF Using NGen

Entity Framework does not come in the default installation of the .net Framework. Therefore, the EF assembly is not NGEN'd by default which means that EF code needs to be JITTED each time the application starts.

[Learn more about Entity Framework 6 and NGEN](https://msdn.microsoft.com/tr-tr/data/dn582034?f=255&MSPPError=-2147217396)