---
layout: post
title: Change Sets
---

A `ChangeSet` describes the delta between two versions of a resource.  It contains a set of statements 
that are added to and a set of statements that are removed from one version of the resource to produce the 
next version.  The resource modified by a `ChangeSet` could be a single individual (Person, 
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
readable because they rely on instances of `rdf:Statement` to express the additions and 
removals.  The Konig ChangeSet Vocabulary, includes terms that allow you to express the
additions and removals using graphs.  

For instance, the ChangeSet from *Example 2* is equivalent to this one:

<div class="example">
  <div class="example-title">ChangeSet</div>
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


