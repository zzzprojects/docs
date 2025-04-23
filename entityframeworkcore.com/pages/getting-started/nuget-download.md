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
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-cloud-download-fill" viewBox="0 0 16 16"><path fill-rule="evenodd" d="M8 0a5.53 5.53 0 0 0-3.594 1.342c-.766.66-1.321 1.52-1.464 2.383C1.266 4.095 0 5.555 0 7.318 0 9.366 1.708 11 3.781 11H7.5V5.5a.5.5 0 0 1 1 0V11h4.188C14.502 11 16 9.57 16 7.773c0-1.636-1.242-2.969-2.834-3.194C12.923 1.999 10.69 0 8 0m-.354 15.854a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 14.293V11h-1v3.293l-2.146-2.147a.5.5 0 0 0-.708.708z"/></svg>
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
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-cloud-download-fill" viewBox="0 0 16 16"><path fill-rule="evenodd" d="M8 0a5.53 5.53 0 0 0-3.594 1.342c-.766.66-1.321 1.52-1.464 2.383C1.266 4.095 0 5.555 0 7.318 0 9.366 1.708 11 3.781 11H7.5V5.5a.5.5 0 0 1 1 0V11h4.188C14.502 11 16 9.57 16 7.773c0-1.636-1.242-2.969-2.834-3.194C12.923 1.999 10.69 0 8 0m-.354 15.854a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 14.293V11h-1v3.293l-2.146-2.147a.5.5 0 0 0-.708.708z"/></svg>
                    NuGet Download
                </a>
            </div>
        </div>
    </div>
</div>
