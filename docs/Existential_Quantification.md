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
> it is the contact point for her at work.


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

<table>
<tr>
<th>Scope</scope>
<td>
Let <code>x</code> be a blank node.  <code>Scope(x)</code> is a subgraph defined recursively 
by the following rules. 
<ol>
	<li> <code>Scope(x)</code> contains every statement that has <code>x</code> as subject or object.</li>
	<li> If <code>y</code> is a blank node connected to <code>x</code> then <code>Scope(y) &sub; Scope(x)</code>.</li>
</ol>
</td>
</tr>
</table>


We say that two blank nodes `a` and `b` are *simply equivalent* if every free variable in the scope of `a` 
can be mapped to a corresponding free variable in the scope of `b`, the scopes are isomorphic under 
the mapping, and `a` maps to `b`.
