---
layout: documentation
title: Existential Quantification
---

Blank nodes within a graph are interpreted as a kind of existential quantification.

Consider these statements in Turtle syntax:

<pre>
	:Alice schema:contactPoint _:b1 .
	_:b1 schema:contactType "work" .
</pre>

Those statements are equivalent to the following statements in English:
 
> There exists some entity that is a contact point of Alice, and
> this entity is the contact point for her at work.


In predicate calculus, one would write:

<pre>
   &#8707;x [ contactPoint(Alice, x) ^ contactType(x, "work") ]
</pre>

where `^` is the symbol for logical conjunction.

In this example, `x` is a quantified variable, and its scope is the set of statements inside the square brackets.

A graph may contain multiple blank nodes.  Suppose we have the following
statements:

<pre>
	:Alice schema:contactPoint _:b1 .
	_:b1 schema:contactType "work" .
	_:b1 schema:address _:b2 .
	_:b2 schema:streetAddress "101 Main Street" ;
	   schema:addressLocality "Springfield" ;
	   schema:addressRegion "Illinois" .
</pre>

Translated into predicate calculus, we have:

<pre>

   &#8707;x &#8707;y [ 
     contactPoint(Alice, x) ^ 
     contactType(x, "work") ^
     address(x, y) ^
     streetAddress(y, "101 Main Street") ^
     addressLocality(y, "Springfield") ^
     addressRegion(y, Illinois)
  ]
</pre>

The scope of the quantified variable `x` is now larger because it is coupled with another quantified variable `y`.
We provide the formal definition for the scope of a blank node below.


Let <code>x</code> be a blank node.  <code>Scope(x)</code> is a subgraph defined recursively 
such that 

1. `Scope(x)` contains every statement that has `x` as subject or object.
2. If `y` is a blank node adjacent to `x` then `Scope(y)` &sub; `Scope(x)`


We say that two blank nodes `a` and `b` are *simply equivalent* if every free variable in the scope of `a` 
can be mapped to a corresponding free variable in the scope of `b`, the scopes are isomorphic under 
the mapping, and `a` maps to `b`.

Since the [graph isomorphism problem](https://en.wikipedia.org/wiki/Graph_isomorphism_problem) 
is classified as NP, it follows that the problem of determining whether two blank nodes are equivalent is
also NP.

There are special cases where the graph isomorphism problem has an efficient solution in polynomial-time. 
A planar graph is one such special case.

A planar graph is a graph that can be drawn in a plane without having any of the edge intersect.

A tree graph is a special



