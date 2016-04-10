---
layout: documentation
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
node is meaningful only in the local document in which the node is described.  The Konig ChangeSet 
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

## Node Equivalence

A `ChangeSet` is useful only if we have some way to map blank nodes from the `reference` graph
to equivalent blank nodes in the graph to be modified.  We need a definition of *node equivalence*.

We say that two named individuals are equivalent if they have the same IRI.
Two literals are equivalent if they have the same lexical value and the same language or same datatype.

Defining the equivalence of blank nodes is tricky.

Let `s` be a blank node described by graph `S`.

Let `t` be a blank node described by graph `T`.

We say that `s` and `t` are equivalent relative to `S` if the following conditions are satisfied:

1.  For each statement of the form `(s p x)` in `S` there is a corresponding statement `(t p y)` in `T` where `x` and `y` are equivalent
2.  For each statement of the form `(u p s)` in `S` there is a corresponding statement `(v p t)` in `T` where `u` and `v` are equivalent

This definition presents some challenges when applied to blank nodes that reference other blank nodes. 
It is challenging because the definition of equivalence becomes circular:  a parent node relies on equivalence statements
about its children, but each child node relies on equivalence statements about its
parents.

Circular conditions present computational challenges, but they can be useful so long as
an effective method exists to check whether the condition holds.  The *[Node Mapping](#node-mapping)* algorithm
is an effective method for identifying equivalent nodes.

## Definitions

<table>
<tr>
	<th>Right Match</th>
	<td>
		We say that blank node <code>s</code> <em>right-matches</em> another node <code>t</code>
		when, for all statements of the form <code>(s&nbsp;p&nbsp;x)</code>, the following conditions are satisfied.
		<ol>
			<li>	If <code>x</code> is a named individual or a literal, then <code>(t p x)</code> is true.</li>
			<li> If <code>x</code> is a blank node, then there exists a node <code>y</code> such that
			<code>(t p y)</code> and <code>(x&nbsp;right&#8209;matches&nbsp;y)</code></li>
		</ol>
	</td>
</tr>

<tr>
	<th>Left Match</th>
	<td>
		We say that blank node <code>s</code> <em>left-matches</em> another node <code>t</code>
		when, for all statements of the form <code>(x&nbsp;p&nbsp;s)</code>, the following conditions are
		satisfied.
		<ol>
			<li> If <code>x</code> is a named individual, then <code>(x p t)</code> is true.</li>
			<li> If <code>x</code> is a blank node, then there exists a blank node <code>y</code> such that
			<code>(y p t)</code> and <code>(x&nbsp;left&#8209;matches&nbsp;y)</code></li>
		</ol>
	</td>
</tr>

<tr>
	<th>Complete Match</th>
	<td>
		We say that blank node <code>s</code> <em>matches</em> another node <code>t</code> completely
		when the following conditions are satisfied.
		<ul>
			<li> For each statement of the form <code>(s p x)</code>:
				<ol>
					<li>If <code>x</code> is a named individual or literal, then <code>(t p x)</code> is true.</li>
					<li>If <code>x</code> is a blank node, then there exists a node <code>y</code> such that
					<code>(t p y)</code> and <code>(x&nbsp;matches&nbsp;y)</code>.</li>
				</ol></li>
			<li> For each statement of the form <code>(x p s)</code>:
				<ol>
					<li>If <code>x</code> is a named individual, then <code>(x p t)</code> is true.</li>
					<li>If <code>x</code> is a blank node, then there exists a node <code>y</code> such that
					<code>(y p t)</code> and <code>(x&nbsp;matches&nbsp;y)</code>.</li>
				</ol>
			</li>
		</ul>
	</td>
</tr>

<tr>
	<th>Path</th>
	<td>
		<p>A <em>Path</em> is a list of statements 
		<code>e<sub>i</sub></code>=(<code>s<sub>i</sub> p<sub>i</sub> o<sub>i</sub></code>)
		such that <code>o<sub>i</sub></code> = <code>s<sub>i-1</sub></code> .</p>
	</td>
</tr>

</table>


## Algorithms

### Check Equivalence

This algorithm takes four arguments as input: a source graph `S`, a node `s` from the source graph, 
a target graph `T`, and a node `t` from the target graph, a graph `M` to which equivalence statements will be added as a side-effect.


1. Let `equivalent-flag = true`.
2. Loop 1: For each statement of the form `(s p x)` in `S`, do:
	1. If `x` is a named individual or literal, do:
		1. If `(t p x)` is not in `T`, do:
			1. Assert `(s not-equivalent t)`
			2. Return.
	2. Otherwise `x` is a blank node.  In this case, do:
		1. Loop 2: For each nodes `y` such that `(t p y)` is true in `T`, do:
			1. CheckEquivalence(S, x, T, y, M).
			2. If `(x not-equivalent y)` do:
				1. Assert `(s not-equivalent t)` in `M`
				2. Return.
	    3. Else if `(x candidate y)`, then set `equivalent-flag = false`.
	  2. End Loop 2.
2. End Loop 1
3. If `equivalent-flag=true` assert `(s right-equivalent t)` 
4. Else assert `(s candidate t)`

## Node Mapping

This algorithm takes two required arguments as input: a source graph `S` and a target graph `T`.
The algorithm returns a mapping from blank nodes in `S` to equivalent blank nodes in `T`.

The mapping is expressed as a set of statements of the form `(s equivalent t)` where
`s` is a blank node in `S` and `t` is a blank node in `T`.


1. Initialize a queue `Q` of statements of the form `(s p x)` from `S` where `s` is a named individual,
  `p` is some predicate, and `x` is a blank node.
2. Loop 1: While `Q` is not empty
	1. Pop a statement `a=(s p x)` from `Q`.
	2. 
	
	
	
   
1. Loop 1: For each blank node `x` in `S`, do:
	1. Loop 2: For each blank node `y` in `T`, do:
		1. If `(x not-equivalent y)` in `M`, continue Loop 2.
	  2. Assert `(x candidate y)` in `M`.
		3. For each statement of the form `(x p u)` in `S` do:
			1. If `u` is a named individual or literal, do:
				1. If `(y p u)` does not exist in `T` then 
					1. Remove statement `(x candidate y)` from `M`.
					2. Add the statement `(x not-equivalent y)` to `M`
					3. Continue Loop 2.
			2. Else `u` is a blank node.  In this case, do:
				1. For each blank node `v` such  that `(y p v)` is true in `T` do:
					1. If `(u not-equivalent v)` in `M`, then:
					1. Remove statement `(x candidate y)` from `M`.
					2. Add the statement `(x not-equivalent y)` to `M`
					3. Continue Loop 2.
						
2.		
  
				
		 



