---
PermaID: 1000207
Name: FluentMap
---

# Dapper.FluentMap

## Overview
Dapper.FluentMap is a small library which allows you to fluently map properties of your domain classes to the database columns. This library functionality is similar to Entity Framework Fluent API and keeps your domain classes clean of mapping attributes.

## Installation
Dapper.FluentMap is available through NuGet: <a href="https://www.nuget.org/packages/Dapper.FluentMap/" target="_blank">https://www.nuget.org/packages/Dapper.FluentMap/</a>

You can easily install this library by running the following command:
```csharp
PM> Install-Package Dapper.FluentMap
```

## APIs
There are two types of mapping supported in Dapper.FluentMap

- Manual mapping
- Convention based mapping

You can map property names manually using the EntityMap<TEntity> class.

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

Initialize your mapping at the start of your application.

```csharp

FluentMapper.Initialize(config =>
{
    config.AddMap(new InvoiceMap());
});

```

Unfortunately, there is no proper documentation available for this library, but you can get a little bit of help from: <a href="https://github.com/henkmollema/Dapper-FluentMap" target="_blank">https://github.com/henkmollema/Dapper-FluentMap</a>

## Support
This library is supported, and you will get your answers within next few days. 

- henkmollema@gmail.com
- <a href="https://github.com/henkmollema/Dapper-FluentMap/issues">https://github.com/henkmollema/Dapper-FluentMap/issues</a>