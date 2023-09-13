---
Name: EFCore-Pinned Versioning
LastMod: 2023-09-12
---

# EFCore-Pinned Versioning

The EFCore-Pinned Versioning is the specific versioning conventions used by both [Entity Framework Extensions](https://entityframework-extensions.net/) and [Entity Framework Plus](https://entityframework-plus.net/) libraries.

To make the explanation below simpler, we will only provide example with [Entity Framework Extensions](https://entityframework-extensions.net/) also named **EFE**.

## What is EFCore-Pinned Versioning?

EFCore-Pinned Versioning extends traditional [Semantic Versioning](https://semver.org/) by introducing a significant adjustment: the addition of the EF Core version at the beginning, shifting all other version components to the right.

Traditional Semantic Versioning follows the format:

- [major].[minor].[patch] (e.g., `7.2.1`)

In contrast, EFCore-Pinned Versioning incorporates the [efcore-version] at the beginning:

- [efcore-version].[major].[minor].[patch] (e.g., `7.100.0.1` for [Entity Framework Extensions](https://entityframework-extensions.net/) using `EF Core 7`)

The [efcore-version] directly corresponds to the EF Core version:

- `7.x.y.z`: For **EFE** using `EF Core 7` 
- `6.x.y.z`: For **EFE** using `EF Core 6` 
- `5.x.y.z`: For **EFE** using `EF Core 5` 

Incrementing the `x` value typically indicates a major release, which often indicate of a breaking changes.

**NOTE:** On NuGet, when the `[patch]` value is `0`, it doesn't appear in the version number. For example, a release like `7.100.0.0` will be displayed on NuGet as `7.100.0`. Keep this in mind when comparing version numbers between our release notes and NuGet listings.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/entityframework-extensions.net/images/efcore-pinned-versioning-example.png" alt="EFCore-Pinned Versioning Example" loading="lazy">

## Why use EFCore-Pinned Versioning?

EFCore-Pinned Versioning provides a mechanism to a library like [Entity Framework Extensions](https://entityframework-extensions.net/) to simultaneously support multiple versions of EF Core while still being able to introduce major version releases.

To concurrently support various product or platform versions, two primary strategies exist:

1. Launch a uniquely named library for each major release of the product/platform.
2. Pin the first version number to the product/platform version.

Considering that EF Core releases a new major version every year, creating a new package of [Entity Framework Extensions](https://entityframework-extensions.net/) can become confusing. The option 2 approach proves more intuitive and accessible.

## Benefits of EFCore-Pinned Versioning

The primary advantages are the ability to support several EF Core versions concurrently within the same package name and to indicate breaking changes with major version increments.

## Why not just use Semantic Versioning?

While [Semantic Versioning](https://semver.org/) serves many projects well, it presents challenges when supporting multiple versions of EF Core under a single package name.

EF Core itself faces challenges with Semantic Versioning. For instance, if EF Core releases version `8.0.0` and later discovers a significant bug necessitating breaking changes, they can't easily transition to version `9.0.0` since the first number of their versioning aligns with the latest .NET release.

In essence, EF Core adopts a **.NET-Pinned Versioning** approach, where the first number corresponds with the most recent .NET version, regardless of the minimum required .NET version (e.g., EF Core 7 requires `.NET 6` and not `.NET 7`).

## How does it apply to Entity Framework 6?

For clarity in the [release notes](https://github.com/zzzprojects/EntityFramework-Extensions/releases), [Entity Framework Extensions](https://entityframework-extensions.net/) for EF6 will always adopt the highest available EF Core version. This means that when .NET 8 / EF Core 8 is released, **EFE** for EF6 will start with the version format `8.x.y.z`.

This can also be viewed as a **.NET-Pinned Versioning** approach, which we discussed in the previous section.

## How are release notes managed?

For simplicity in our [release notes](https://github.com/zzzprojects/EntityFramework-Extensions/releases), all modifications are listed under the highest available stable EF Core version. Even if a change only affects [Entity Framework Extensions](https://entityframework-extensions.net/) using EF Core 6, we release packages for all EF Core versions, adding the changes to the notes under the latest release.

For example, release notes might be displayed under `7.100.0.1` (the `patch` part was incremented to `1`), even if the fix was exclusively for [Entity Framework Extensions](https://entityframework-extensions.net/) using EF Core 6.

## Who Adopts EFCore-Pinned Versioning?

While we can't pinpoint every library's exact versioning strategy, several third-party EF Core libraries have adopted a similar approach:

- [Entity Framework Extensions](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
- [Entity Framework Plus](https://www.nuget.org/packages/Z.EntityFramework.Plus.EFCore)
- [EF Core Dynamic Linq](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.DynamicLinq)

## EFCore-Pinned Versioning in Action

The [Entity Framework Extensions](https://entityframework-extensions.net/) library introduced a breaking change in its [release on 2023-09-12](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/7.100.0.0). Highlighted features of this release included:

- The formal adoption of **EFCore-Pinned Versioning**.
- A major breaking changes within the [IncludeGraph feature](https://entityframework-extensions.net/v7-100-0-0-include-graph).

These modifications was including a breacking changes, consequently requiring the major version being incremented to `100` resulting in the release of the following packages:

- [v7.100.0.0](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/7.100.0.0): for **EFE** using `EF Core 7`
- [v6.100.0.0](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/6.100.0.0): for **EFE** using `EF Core 6`
- [v5.100.0.0](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/5.100.0.0): for **EFE** using `EF Core 5`
- [v3.100.0.0](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/3.100.0.0): for **EFE** using `EF Core 3`
- [v2.100.0.0](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/2.100.0.0): for **EFE** using `EF Core 2`
- [v7.100.0.0](https://www.nuget.org/packages/Z.EntityFramework.Extensions/7.100.0.0): for **EFE** using `EF6`