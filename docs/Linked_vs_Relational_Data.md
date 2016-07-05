---
layout: documentation
title: Linked vs Relational Data
---

There are two main ways to go about building a data model.  The first approach
follows patterns familiar from relational database design.  The second approach
follows linked data principles at the heart of the semantic web.  This article
describes the two approaches and explains how to move back and forth between
the two approaches.

## Relational Models

A relational database stores entities in tables.  Relationships between entities
are expressed either through the use of foreign keys or association tables.  
This style of representing relationships often bubbles up into contracts for
exchanging data between systems.  For instance, we might have a JSON payload
that describes a Person named Alice like this:

```json
{
  "guid" : "EBk0Mat6VR",
  "givenName" : "Alice",
  "familyName" : "Jones",
  "worksFor_guid" : "OWdCTHRT9z"
}
```
In this example, the `OWdCTHRT9z` is a foreign key reference to
the company that Alice works for.  One must somehow come to understand that
the `worksFor_guid` property takes as its value the primary key for an entity of
type `Organization`.  

Typically, this information is expressed in documentation.  Developers
read the documentation and write code that complies with the design.

If we want machines to reason about this data structure, though, we need a more
formal data model.  We need to express the data model in a language that
machines can understand.  We can use the W3C OWL standard to describe the
property, and the Konig Ontology Language to express the fact that it is a
foreign key property, as shown in the following snippet.

```
ex:worksFor_guid a kol:ForeignKeyProperty ;
  rdfs:comment "A foreign key reference for the Organization that this Person works for"
  rdfs:domain schema:Person ;
  rdfs:range xsd:string ;
  kol:referencedClass schema:Organization ;
  kol:keyProperty kol:guid
  .
```

In plain English, this specification asserts the following statements:

* `worksFor_guid` is a Foreign Key Property.
* It applies to entities of type `Person`
* Its value is a string that references an entity of type `Organization`
* In particular, the value is the `guid` property of the referenced `Organization`

Now the semantics of this property are abundantly clear.

## Linked Data Models

As defined by Wikipedia, [Linked Data](https://en.wikipedia.org/wiki/Linked_data)
is a method of publishing structured data so that it can be interlinked
and become more useful through semantic queries.  Linked Data principles discourage
the use of foreign keys in a data model.  It takes a more object-oriented approach.
You define relationships between objects, and those objects appear in your
data contracts, as shown below.

```json
{
  "guid" : "EBk0Mat6VR",
  "givenName" : "Alice",
  "familyName" : "Jones",
  "worksFor" : {
    "guid" : "OWdCTHRT9z"
  }
}
```

In this example, the `worksFor` property defines a direct relationship between
a Person and an Organization.  The data contract embeds a tiny *view* of the
Organization that includes its `guid`.  In some ways, this is a more natural
representation of the relationship.  Indeed, the formal specification of the
relationship is simpler, as shown below:

```
schema:worksFor a owl:ObjectProperty ;
  rdfs:comment "The Organization that this Person works for"
  rdfs:domain schema:Person ;
  rdfs:range schema:Organization
  .  
```

In plain English, this specification makes the following statements:

* The `worksFor` property is a relationship between two objects.
* It applies to entities of type `Person`
* It's value is an entity of type `Organization`

## Data Model Equivalence

Clearly, the two data models presented above equivalent.  If you have one
JSON document you can easily transform it into the other.  But, given the
formal specifications alone, there is no way to know that `worksFor_guid` and
`worksFor` capture the same semantic relationship.  We can enhance the specifications
with this information as shown below:

```
ex:worksFor_guid a kol:ForeignKeyProperty ;
  rdfs:comment "A foreign key reference for the Organization that this Person works for"
  rdfs:domain schema:Person ;
  rdfs:range xsd:string ;
  kol:referencedClass schema:Organization ;
  kol:keyProperty kol:guid ;
  kol:equivalentObjectProperty schema:worksFor
  .
```
The last line in this snippet tells us that `worksFor_guid` and `worksFor` are
semantically equivalent.

## Equivalent Data Shapes

So far, we have been looking at definitions for individual properties.  
But we also need to consider the structure of data records that represent entities in flight or at rest.  SHACL is an emerging standard from W3C that provides a convenient
way to describe data structures.

Consider the following snippets which show how to express the two alternative
structures that we presented above.

### Relational Data Shape

```
<RelationalPersonShape> a sh:Shape ;
  sh:scopeClass schema:Person ;
  sh:property [
    sh:predicate kol:guid ;
    sh:datatype xsd:string ;
    sh:minCount 1 ;
    sh:maxCount 1
  ], [
    sh:predicate schema:givenName ;
    sh:datatype xsd:string ;
    sh:minCount 1 ;
    sh:maxCount 1
  ], [
    sh:predicate schema:familyName ;
    sh:datatype xsd:string ;
    sh:minCount 1 ;
    sh:maxCount 1
  ], [
    sh:predicate schema:worksFor_guid ;
    sh:datatype xsd:string ;
    sh:minCount 1 ;
    sh:maxCount 1
  ]
  .
```

  ### Linked Data Shape

  ```
  <LinkedDataPersonShape> a sh:Shape ;
    sh:scopeClass schema:Person ;
    kol:equivalentRelationalShape <RelationalPersonShape> ;
    sh:property [
      sh:predicate kol:guid ;
      sh:datatype xsd:string ;
      sh:minCount 1 ;
      sh:maxCount 1
    ], [
      sh:predicate schema:givenName ;
      sh:datatype xsd:string ;
      sh:minCount 1 ;
      sh:maxCount 1
    ], [
      sh:predicate schema:familyName ;
      sh:datatype xsd:string ;
      sh:minCount 1 ;
      sh:maxCount 1
    ], [
      sh:predicate schema:worksFor ;
      sh:minCount 1 ;
      sh:maxCount 1 ;
      sh:valueShape [
        sh:scopeClass schema:Organization ;  
        sh:property [
            sh:predicate kol:guid ;
            sh:datatype xsd:string ;
            sh:minCount 1 ;
            sh:maxCount 1
          ]
      ]
    ]
    .
```

The Konig toolkit provides methods for generating a Relational shape from a linked
data shape.  
