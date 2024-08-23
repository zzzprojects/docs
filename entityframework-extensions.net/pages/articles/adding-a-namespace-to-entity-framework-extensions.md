---
Title: Adding a Namespace to Entity Framework Extensions
MetaDescription: Learn how to add a namespace to extension methods from Entity Framework Extensions
LastMod: 2024-08-23
---

# Adding a Namespace to Entity Framework Extensions

By default, Entity Framework Extensions does not include a namespace for methods like [Bulk Insert](https://entityframework-extensions.net/bulk-insert) or others [Bulk Extensions](https://entityframework-extensions.net/bulk-extensions).

When we created this library, our primary goal was to make Entity Framework Extensions feel part of Entity Framework (both EF Core and EF6). We wanted users to be able to download the NuGet package and start using the library immediately, without needing to add any additional namespaces. This approach is different from libraries like [Dapper](https://www.learndapper.com/) or [EF Core](https://www.learnentityframeworkcore.com/) itself, where adding a namespace is required. With our library, the intent was for it to integrate directly in Entity Framework.

Is this approach ideal? Is it a mistake? There are certainly arguments on both sides, with valid pros and cons. However, one thing is clear from our perspective: the library has functioned without requiring namespaces since we created it in 2014, and changing this behavior now would result in major breaking changes for all our customers.

In this article, we will walk through the steps to add a namespace to Entity Framework Extensions for EF Core by using an [extern alias](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/extern-alias) and adding a single file to your projet.

**NOTE:** This solution is currently available only for EF Core. If there is sufficient demand, we may extend similar support for EF6.

## How to Add a Namespace to All Extension Methods from Entity Framework Extensions (EFE)

In this section, we'll walk you through a simple four-step process to add a namespace to all extension methods from Entity Framework Extensions (EFE).

### Step 1 - Add the Entity Framework Extensions NuGet Package

The first step is straightforward. [Download EFE](https://entityframework-extensions.net/download) and add the NuGet package to your project.

**Note:** Make sure to add this NuGet package to all projects where you want to use a namespace for our extension methods.

### Step 2 - Specify an Alias for the Entity Framework Extensions Package

Open the properties window for the Entity Framework Extensions package in each project where you want to use a namespace, and specify the following value for the **Aliases** property: `EntityFrameworkExtensionsCoreAlias`.


<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/entityframework-extensions.net/images/efcore-namespace-aliases.jpg" alt="EF Core namespaces aliases">


### Step 3 - Download and Add the "EntityFrameworkExtensionsCoreNamespace.cs" File to Your Project

Download the [EntityFrameworkExtensionsCoreNamespace.cs](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/src/EntityFrameworkExtensionsCoreNamespace.cs) file and add it to your project.

### Step 4 - Start Using Our Library with the "EntityFrameworkExtensionsCoreNamespace" Namespace

To begin using our library with a namespace, simply include the `EntityFrameworkExtensionsCoreNamespace` using directive in any file where you want to access our methods.

```csharp
using EntityFrameworkExtensionsCoreNamespace;

using (var context = new EntityContext())
{
    context.BulkInsert(customers);
}
```

And voilà!

## Additional Information

In some cases, you may need to use classes that are not extension methods, such as when adding a license through `Z.EntityFramework.Extensions.LicenseManager`. However, since we've added the **EntityFrameworkExtensionsCoreAlias** alias, these classes are no longer directly accessible.

To access these classes, you can still do so by first adding the `extern alias EntityFrameworkExtensionsCoreAlias;` directive, and then prefixing the namespace with this alias, like so: `EntityFrameworkExtensionsCoreAlias.Z.EntityFramework.Extensions.LicenseManager`.

Here's an example:

```csharp
extern alias EntityFrameworkExtensionsCoreAlias;

EntityFrameworkExtensionsCoreAlias.Z.EntityFramework.Extensions.LicenseManager.AddLicense("[LicenseName]", "[LicenseKey]");

var audit = new List<EntityFrameworkExtensionsCoreAlias.Z.BulkOperations.AuditEntry>();
```

## Conclusion

With this technique, adding a namespace to Entity Framework Extensions becomes straightforward. While there are some pros and cons—such as occasionally needing to add the `extern alias` directive, it provides a viable alternative solution for those who prefer or require namespaces.

If you encounter any other scenarios or issues that we should be aware of, please report them to our support team at: info@zzzprojects.com.