---
title: The InverseProperty Attribute
description: Usage of the InverseProperty Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/inverseproperty-attribute
status: Published
lastmod: 2025-07-11
---

# EF Core InverseProperty Attribute

The `InverseProperty` attribute is used to denote the inverse navigation property of a relationship when the same type takes part in multiple relationships. The following model depicts a contact system where the user who creates and updates contact records are recorded:

```csharp
public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public User CreatedBy { get; set; }
    public User UpdatedBy { get; set; }
}

public class User
{
    public int UserId { get; set; }
    public string UserName { get; set; }
    public List<Contact> ContactsCreated { get; set; }
    public List<Contact> ContactsUpdated { get; set; }
}
```
This model appears to fit the [inverse navigation property convention](/relationships/managing-one-to-many-relationships#inverse-navigation-property) for Entity Framework Core, but EF Core will raise an error when asked to map these relationships:


> Unable to determine the relationship represented by navigation property 'Contact.CreatedBy' of type 'User'. Either manually configure the relationship, or ignore this property from the model.

The `InverseProperty` attribute is applied to the navigation properties in the `User` class to specify their corresponding inverse navigation properties in the `Contact` class:

```csharp
public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public User CreatedBy { get; set; }
    public User UpdatedBy { get; set; }
}

public class User
{
    public int UserId { get; set; }
    public string UserName { get; set; }
    [InverseProperty("CreatedBy")]
    public List<Contact> ContactsCreated { get; set; }
    [InverseProperty("UpdatedBy")]
    public List<Contact> ContactsUpdated { get; set; }
}
```

#### Fluent API
The Fluent API options for configuring inverse navigation properties are the `WithRequired`, `WithOptional`, and `WithMany` methods.

https://msdn.microsoft.com/en-us/data/jj591620.aspx
