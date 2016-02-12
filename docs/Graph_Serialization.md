---
layout: documentation
title: Graph Serialization
position: 2
---

There are many formats for serializing the information in an RDF `Graph`.
In this article, we discuss three of the most popular formats: N-Triples, Turtle, and JSON-LD.

To be concrete we'll show how to serialize a graph like this one:

![Social Network](/images/alice-bob-parent-names.png) 


## N-Triples

As we [discussed earlier](/RDF-in-a-nutshell), `Individuals` and relationships (i.e. `Properties`)
in an RDF `Graph` are named by IRI values.  Suppose we assign IRI values as shown below. 


![Social Network with IRI labels](/images/alice-bob-parent-iris.png)

With these IRI assignments, the `Statements` in the graph can be represented as a series of 
`(subject, predicate, object)` triples like this:

<div class="before-small-table"></div>
~~~
<http://example.com/Alice> <http://purl.org/vocab/bio/0.1/parent> <http://example.com/John>   .
<http://example.com/Alice> <http://purl.org/vocab/bio/0.1/parent> <http://example.com/Susan>  .
<http://example.com/knows> <http://xmlns.com/foaf/0.1/knows>      <http://example.com/Bob>    .
<http://example.com/Bob>   <http://purl.org/vocab/bio/0.1/parent> <http://example.com/Richard>.
<http://example.com/Bob>   <http://purl.org/vocab/bio/0.1/parent> <http://example.com/Martha> .
<http://example.com/Bob>   <http://schema.org/birthDate>          "1994-08-07"                .
~~~ 

This is an example of a graph in **N-Triples** format.

## Turtle

The N-Triples format is extremely simple, but also extremely verbose.  Every URI must be spelled out completely as a fully-qualified URI.

The Turtle format is more compact.  Our social network in Turtle syntax looks like this:

~~~
@base <http://example.com/> .
@prefix bio: <http://purl.org/vocab/bio/0.1/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

Alice bio:parent Susan , John ;
   foaf:knows Bob .

Bob bio:parent Martha , Richard ;
   schema:birthDate "1994-08-07" .
~~~
  
One can define prefixes so that a compact URI like `bio:parent` is understood to be a shortened form of the fully-qualified URI `http://purl.org/vocab/bio/0.1/parent`.

One can define a base URI, so that a relative URI like `Alice` is understood to be a shortened form of the fully-qualified URI `http://example.com/Alice`.

The Turtle format introduces punctuation.  A comma (,) means that the next statement has the same subject and object as the previous statement.  So the line...

~~~
Alice bio:parent Susan, John .
~~~

...expresses two statements:

~~~
Alice bio:parent Susan .
Alice bio:parent John .
~~~


A semicolon (;) means that the next statement has the same subject as the previous statement, but a different predicate and object.  So the line...

~~~
Alice bio:parent John ; schema:birthDate "1994-08-07" .
~~~

...expresses two statements:

~~~
Alice bio:parent John .
Alice schema:birthDate "1994-08-07" .
~~~


## JSON-LD

JSON-LD is a syntax for serializing a graph using JSON, where the "LD" suffix stands for "Linked Data".
Here's how the same social network could be encoded in JSON-LD.

~~~
{ "@context" : {
    "bio" : "http://purl.org/vocab/bio/0.1/",
    "foaf" : "http://xmlns.com/foaf/0.1/",
    "schema" : "http://schema.org/",
    "parent" : { "@id" : "bio:mother", "@type" : "@id"},
    "knows" : { "@id" : "foaf:knows", "@type" : "@id" },
    "birthDate" : { "@id" : "schema:birthDate", "@type" : "schema:DateTime"},
    "@base" : "http://example.com/"
  }, {
  
  "@id" : "Alice",
  "parent" : ["Susan", "John"],
  "knows" : {
    "@id" : "Bob",
    "parent" : ["Martha", "Richard"],
    "birthDate" : "1994-08-07"
  }
}
~~~
The `@context` object describes the terms that will be used in the remainder of the JSON document.  It 
maps simple names for terms (`mother`, `father`, `knows`, `birthDate`) to the fully-qualified URI for 
those terms. The context may be externalized and referenced by a URI as shown in the following example:

~~~
{ "@context" : "http://example.com/contexts/social-network",
 
  "@id" : "Alice",
  "parent" : ["Susan", "John"],
  "knows" : {
    "@id" : "Bob",
    "parent" : ["Martha", "Richard"],
    "birthDate" : "1994-08-07"
  }
}
~~~

In this example, `http://example.com/contexts/social-network` is the address at which one may look 
up the context.
 