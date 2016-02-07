---
layout: post
title: Change Sets
---

A `ChangeSet` describes the delta between two versions of a resource.  It contains a set of statements 
that are added to and a set of statements that are removed from one version of the resource to produce the 
next version.  The resource modified by a `ChangeSet` may be a single individual (Person, 
Place, Thing, etc.) or a graph of individuals.

Suppose you start with the following resource...
<div class="example">
	<div class="example-title">Original Resource</div>
	<pre class="json">
  {
    "@context" : "http://schema.org/",
    "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
    "givenName" : "Theodore",
    "familyName" : "Roosevelt"
  }
	</pre>
</div>

...and you apply a `ChangeSet` like this:

<div class="example">
  <div class="example-title">ChangeSet</div>
  <pre class="json">
  {
    "@context" : [
      "http://www.konig.io/ctx/changeset",
      "http://schema.org/"
    ],
    "removal" : [{
      "subject" : "http://dbpedia.org/resource/Theodore_Roosevelt",
      "predicate" : "schema:givenName",
      "object" : "Theodore"
    }],
    "addition" : [{
      "subject" : "http://dbpedia.org/resource/Theodore_Roosevelt",
      "predicate" : "schema:givenName",
      "object" : "Teddy"
    },{
      "subject" : "http://dbpedia.org/resource/Theodore_Roosevelt",
      "predicate" : "schema:birthDate",
      "object" : "1858-10-27"
    }]
  }
  </pre>
</div>

Then, the resulting resource would be...

<div class="example">
  <div class="example-title">Final Resource</div>
  <pre class="json">
  {
    "@context" : "http://schema.org/",
    "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
    "givenName" : "Teddy",
    "familyName" : "Roosevelt",
    "birthDate" : "1858-10-27"
  }
  </pre>
</div>

As you can see in *Example 2*, traditional ChangeSets tend to be extremely verbose and not very
readable because they rely on instances of `rdf:Statement` to express additions and 
removals.  The [Konig ChangeSet Vocabulary](http://www.konig.io/ns/kcs/) includes terms that allow you to express 
additions and removals using graphs.  In JSON-LD, this style of describing a `ChangeSet` is
arguably more natural. 

For instance, the ChangeSet from *Example 2* is equivalent to this one:

<div class="example">
  <div class="example-title">Equivalent ChangeSet</div>
  <pre class="json">
  {
    "@context" : [
      "http://www.konig.io/ctx/changeset",
      "http://schema.org/"
    ],
    "remove" : { "@graph" : [{
      "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
      "givenName" : "Theodore"
    }]},
    "add" : { "@graph" : [{
      "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
      "givenName" :  "Teddy",
      "birthDate" : "1858-10-27"
    }]}
  }
  </pre>
</div>

## Blank Nodes

A traditional `ChangeSet` cannot describe changes to blank nodes because the identifier for a blank 
node is unique only in the local document in which the node is described.  The Konig ChangeSet 
Vocabulary addresses this problem.  It introduces the notion of a *reference* graph which provides 
enough information to uniquely identify blank nodes that have been modified.

We'll explain how it works with another example.  Suppose we have the following description of
Teddy Roosevelt which includes information about his `birthPlace`.

<div class="example">
  <div class="example-title">Roosevelt v.3</div>
  <pre class="json">
  {
    "@context" : "http://schema.org/",
    "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
    "givenName" : "Teddy",
    "familyName" : "Roosevelt",
    "birthDate" : "1858-10-27",
    "birthPlace" : {
      "description": "The house where Roosevelt first lived.",
      "address" : {
        "streetAddress" : "28 East 20th Street",
        "addressLocality" : "Manhattan",
        "addressRegion" : "NY"
    }}
  }
  </pre>
</div>

Later we decide to use geo-coordinates instead of a postal address,
and so we apply the following `ChangeSet`.


<div class="example">
  <div class="example-title">Replace postal address with geo-coordinates</div>
  <pre class="json">{
    "@context" : [
      "http://www.konig.io/ctx/changeset",
      "http://schema.org/"
    ],
    "reference" : { "@graph" : [{
      "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
      "birthPlace" : "_:b1"
    }]},
    "remove" : { "@graph" : [{
      "@id" : "_:b1",
      "address" : {
        "streetAddress" : "28 East 20th Street",
        "addressLocality" : "Manhattan",
        "addressRegion" : "NY"
    }}]},
    "add" : { "@graph" : [{
      "@id" : "_:b1"
      "geo" : {
        "latitude" : 40.738889,
        "longitude" : -73.989722
    }}]}
  }
  </pre>
</div>

The `reference` graph tells us that `_:b1` is the identifier for Teddy's 
birth place.

After applying the `ChangeSet`, we have the following description:

<div class="example">
  <div class="example-title">Roosevelt v.4</div>
  <pre class="json">
  {
    "@context" : "http://schema.org/",
    "@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
    "givenName" : "Teddy",
    "familyName" : "Roosevelt",
    "birthDate" : "1858-10-27",
    "birthPlace" : {
      "description": "The house where Roosevelt first lived.",
      "geo" : {
        "latitude" : 40.738889,
        "longitude" : -73.989722
  }}}
  </pre>
</div>

## Usage Rules

The following list describes the best practices for using `ChangeSet`s.
  
1. If the ChangeSet adds a new blank node, do not list any statements about that node in the `reference` graph.
  Recall that the `reference` graph contains information about the prior state. If you are
  adding a new blank node, then it had no prior state, and so it should not appear in the 
  `reference` graph.
  
2. If a blank node is mutated by the ChangeSet, then the `reference` graph (or equivalently,
   the set of `trace` statements) must contain enough information to uniquely identify the node.  There are several
   options for identifying a blank node:
      
   1. **Inverse Functional Property**.  If a mutated blank node `n` contains an inverse functional property
      `p` then it is sufficient to provide a single triple of the form <br> `(n p o)` where `o`
      is the value of the inverse functional property.
      
   2. **Composite Key**.  If a mutated blank node `n` is an instance of an `owl:Class` that declares a
      list of unique keys via the `owl:hasKey` property, and `n` has a value for each property in the
      list, then for each property `p` from the list of unique keys, include a triple of the form
      `(n p o)` where `o` is the value of the property `p` on `n`.
      
   3. **Functional Property**.  Suppose a mutated blank node `n` is the object of some functional property `p` 
      on a resource `s`.  Then include the triple `(s p n)` in the `reference` graph.  If `s` is
      a blank node, then include enough information about `s` to identify it uniquely (i.e. apply the
      usage rules to `s`).
      
   4. **Brute Force Method**. Include a complete description of the mutated node in its prior state.
      That is, include every statement that contains the node as either the subject or object.
      If the node is related to another blank node, then include enough information to 
      uniquely identify the related node, too.  


