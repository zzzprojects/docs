---
PermaID: 100003
Name: Host NuGet Packages
---

# Host NuGet Packages

Sometimes, you might want to release packages to only a limited audience instead of making packages publicly available, such as your organization or workgroup. 

 - Some organization may want to restrict which third-party libraries their developers may use, and thus direct those developers to draw from a limited package source rather than nuget.org.
 - Creating a private NuGet feed gives you an easy way to standardize versioning and distribution of framework or library code in your organization.

NuGet supports setting up private package sources in the following ways.

 - **[Local feed](/loacal-feed):** Packages are simply placed on a suitable network file share, ideally using nuget init and nuget add to create a hierarchical folder structure.
 - **[NuGet.Server:](/nuget-server)** Packages are made available through a local HTTP server.
