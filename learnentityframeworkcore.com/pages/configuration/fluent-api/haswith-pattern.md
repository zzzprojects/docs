---
title: The Has/With Pattern For Configuring Relationships
description: The Has/With pattern for configuring relationships in Entity Framework Core
canonical: /configuration/fluent-api/haswith-pattern
status: Published
lastmod: 2023-02-27
---

# EF Core Has/With Pattern For Configuring Relationships

When configuring relationships with the Fluent API, you will use the _Has/With_ pattern. The _Has_ side of the pattern is represented by the `HasOne` and `HasMany` methods. The _With_ side of the relationship is represented by the `WithOne` and `WithMany` methods.

The `HasOne` method is used for reference [navigation properties](/relationships#navigation-properties) as is the `WithOne` method. The `HasMany` and `WithMany` methods are used for collection [navigation properties](/relationships#navigation-properties).

A `Has*` method must be combined with a `With*` method to configure a valid relationship.

