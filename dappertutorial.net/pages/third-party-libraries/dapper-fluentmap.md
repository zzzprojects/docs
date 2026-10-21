---
PermaID: 1000207
Title: Dapper.FluentMap - A Getting Started Tutorial for Mapping
MetaDescription: Learn to use the actively maintained Dapper.FluentMap library for explicit and convention-based column mapping, with installation, documentation, support, and migration guidance.
LastMod: 2026-10-20
---

# Dapper.FluentMap: A Getting Started Tutorial for Mapping

## Overview

**Dapper.FluentMap** is a small library that allows you to fluently map properties of your domain classes to the database columns. This library functionality is similar to Entity Framework Fluent API and keeps your domain classes clean of mapping attributes.

 - It is straightforward to set up; just create a map class for each of your domain model and database model classes, and configure the mappings using the fluent API. 
 - Once configured, FluentMap lets Dapper map database result columns to properties on your domain classes.
 - By default, Dapper will map an object's properties to columns with the same name. 
 - However, sometimes you might want to map an object's properties to columns with different names. 
 - It allows you to configure these mappings in a fluent, concise way.

## Project Status

**Dapper.FluentMap is actively maintained.** The original repository, created by Henk Mollema, has moved to [rodri-oliveira-dev/Dapper-FluentMap](https://github.com/rodri-oliveira-dev/Dapper-FluentMap). The maintained 3.x release line continues the core mapping APIs while introducing optional capabilities. Version [3.6.0](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/releases/tag/v3.6.0) was published on October 13, 2026; check [GitHub Releases](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/releases) for the latest version.

## NuGet Installation

**Dapper.FluentMap** is available through NuGet: <a href="https://www.nuget.org/packages/Dapper.FluentMap/" target="_blank">https://www.nuget.org/packages/Dapper.FluentMap/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper.FluentMap
```

## APIs

There are two types of mapping supported in **Dapper.FluentMap**.

- Manual mapping
- Convention-based mapping

You can map property names manually using the `EntityMap<TEntity>` class.

```csharp

public class InvoiceMap : EntityMap<Invoice>
{
    public InvoiceMap()
    {
        // Map property 'InvoiceID' to column 'Id'.
        Map(i => i.InvoiceID).ToColumn("Id");

        // Ignore the 'Detail' and 'Items' properties when mapping.
        Map(i => i.Detail).Ignore();
        Map(i => i.Items).Ignore();
    }
}

```

Initialize your mapping at the start of your application. The existing `EntityMap<TEntity>` and `FluentMapper.Initialize(...)` APIs remain supported in the 3.x line.

```csharp
FluentMapper.Initialize(config =>
{
    config.AddMap(new InvoiceMap());
});
```

## Documentation

The maintained project provides the following documentation:

- [README and getting started](https://github.com/rodri-oliveira-dev/Dapper-FluentMap#readme)
- [Usage examples](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/blob/main/USAGE.md)
- [Migration guide from 2.x to 3.x](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/blob/main/MIGRATION.md)
- [Compatibility and supported environments](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/blob/main/COMPATIBILITY.md)

The 3.x line also adds opt-in features such as mapping profiles, immutable-object materialization, isolated runtimes, and optional dependency-injection, Roslyn analyzer, and source-generator packages. Refer to the project documentation for their supported scenarios and limitations.

## Support

For questions, bug reports, and feature requests, use the [current GitHub Issues page](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/issues). For version-specific changes, see [GitHub Releases](https://github.com/rodri-oliveira-dev/Dapper-FluentMap/releases). Support is community-maintained; response times are not guaranteed.