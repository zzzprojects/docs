---
PermaID: 1000053
Name: Relationships
---

# Relationships

In the context of relational databases, a relationship exists between two relational database tables when one table has a foreign key that references the primary key of the other table. In Entity Framework, a relationship defines how two entities relate to each other.

Entity framework supports three types of relationships.

 - <a href="{{ site.github.url }}/one-to-one-relationship">One-to-One</a>
 - <a href="{{ site.github.url }}/one-to-many-relationship">One-to-Many</a>
 - <a href="{{ site.github.url }}/many-to-many-relationship">Many-to-Many</a> 

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/relationships.png">

## One-to-One Relationship

 - In a one-to-one relationship, each row of data in one table is linked to zero or one row in the second table.
 - For example, the `Author` and `AuthorBiography` have a one-to-one relationship.

<a href="{{ site.github.url }}/one-to-one-relationship">Read more about One-to-One Relationship</a>

## One-to-Many Relationship

 - In a one-to-many relationship, each row of data in one table is linked to one or more rows in the second table.
 - For example, the `Author` and `Book` have a one-to-many relationship.

<a href="{{ site.github.url }}/one-to-many-relationship">Read more about One-to-Many Relationship</a>

## Many-to-Many Relationship

 - In a many-to-many relationship, each row of data in one table is linked to many rows in the second table and vice versa.
 - For example, the `Book` and `Category` have a many-to-many relationship that is defined by one-to-many relationship from each of these tables to the `BookCategory` table.

<a href="{{ site.github.url }}/many-to-many-relationship">Read more about Many-to-Many Relationship</a>