---
Permalink: overview
Name: Overview
IsHome: 1
---

# Overview

Database applications often rely on Dynamic SQL queries that are constructed at run-time through program logic. The LINQ infrastructure supports similar capabilities through expression trees, which are an appropriate abstraction for a variety of scenarios, but for others, a string-based representation may be more convenient.

- The dynamic query library relies on a simple expression language for formulating expressions and queries in strings.
- It provides you with string-based extension methods that you can pass any string expression into instead of using language operators or type-safe lambda extension methods.
- It is simple and easy to use and is particularly useful in scenarios where queries are entirely dynamic, and you want to provide an end-user UI to help build them.

Using this Dynamic LINQ library, we can do the following:

- Dynamic string-based querying through LINQ providers.
- Dynamic parsing of strings to produce expression trees such as `ParseLambda` and `Parse` methods.
- Dynamic creation of **Data Classes** using the `CreateType` method.
