---
permaid: 1000222
Title: EF Core NuGet - Learn Where and How to Install the Package
MetaDescription: Unlock the power of EF Core by getting started to download the package. Learn which NuGet package to download and how to install it in your project.
LastMod: 2023-02-21
tags: getting-started nuget download
---

# EF Core NuGet: Discover Where and How to Install the Package

## Install EF Core

Entity Framework Core is a free and open source, and it can be installed through NuGet. 

<div class="row">
    <div class="col-lg-6">
        <div class="card card-layout-z2 wow slideInLeft">
            <div class="card-body wow slideInUp">
                <a class="btn btn-lg btn-z" role="button" href="https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/" onclick="ga('send', 'event', { eventAction: 'download'});" style="visibility: visible; animation-name: pulse;">
                    <i class="fa fa-cloud-download" aria-hidden="true"></i>
                    NuGet Download
                </a>
            </div>
        </div>
    </div>
</div>

When you install Microsoft.EntityFrameworkCore, you will discover that it is the absolute core of EF Core and it doesn't have additional logic that won't apply to all scenarios. 

For example, EF Core will need to know what database or datastore you plan on working with and those providers are in individual packages. 

To bring EF Core, it is easier to start by targeting the datastore you are going to be working with and let NuGet's dependencies pull in the rest, including the base Entity Framework Core package. 

For SQL Server Local DB, which is installed with Visual Studio, we need to install **Microsoft.EntityFrameworkCore.SqlServer** and will get all the packages required for EF Core.

<div class="row">
    <div class="col-lg-6">
        <div class="card card-layout-z2 wow slideInLeft">
            <div class="card-body wow slideInUp">
                <a class="btn btn-lg btn-z" role="button" href="https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/" onclick="ga('send', 'event', { eventAction: 'download'});" style="visibility: visible; animation-name: pulse;">
                    <i class="fa fa-cloud-download" aria-hidden="true"></i>
                    NuGet Download
                </a>
            </div>
        </div>
    </div>
</div>
