# Overview

## What is Blazor?

The ASP.NET Team has created a new set of tooling called Blazor. It is a framework for client-side applications written in .NET, running under WebAssembly. 

 - It is based on existing web technologies like HTML and CSS, but you use C# and Razor syntax instead of JavaScript to build composable web UI.
 - It gives all the benefits of a rich, modern single-page application (SPA) platform while using .NET end-to-end.
 - The idea about Blazor is to be able to combine Razor and C# into a client-side web project that runs completely in the browser.

## WebAssembly

WebAssembly is a binary format for the code in the browser, and it runs much faster than traditional JavaScript. It gives the browser several advantages, such as:

 - Runs at near-native performance
 - Runs in a memory-safe, sandbox
 - It compiles from a range of languages, i.e., .NET, C, C++, Java, Rust, etc.

The main advantage of WebAssembly is that it handles memory-rich jobs and multi-threading very well as compared to javascript.

## Features

Blazor contains all the features of a modern web framework such as:

 - A component model for building composable UI
 - Routing
 - Layouts
 - Forms and validation
 - Dependency injection
 - Live reloading in the browser during development
 - Server-side rendering
 - Full .NET debugging both in browsers and in the IDE
 - Rich IntelliSense and tooling
 - Publishing and app size trimming

## Setup

 - Download and install the [.NET Core 2.1 SDK (2.1.300 or later)](https://www.microsoft.com/net/learn/get-started-with-dotnet-tutorial).
 - Download and install [Visual Studio 2017 (15.7 or later)](https://visualstudio.microsoft.com/vs/) with the ASP.NET and web development workload selected.
 - Download and install the latest [ASP.NET Core Blazor Language Services](https://marketplace.visualstudio.com/items?itemName=aspnet.blazor) extension from the Visual Studio Marketplace.
