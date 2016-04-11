---
layout: documentation
title: Robustness
---

Traditional object-oriented programming languages like Java or C# use [frame-based](https://en.wikipedia.org/wiki/Frame_(artificial_intelligence)) knowledge representations. 
In a frame-based representation, each class defines a fixed set of slots (i.e. fields) for
the properties of an object.  A class may inherit properties from ancestors within the class
hierarchy, but the total set of slots is fixed.  There is one and only one 
representation for any given class.

By contrast, semantic models focus on atomic *statements* about a resource.  Each statement 
is a triple of the form:

```
	<subject> <predicate> <object> .
```

One can construct many different representations (i.e. descriptions) of a resource.
Each description conveys a different set of statements.  At bottom, a data contract
as merely one particular view of a resource or set of resources.  When designing a data 
contract, you are choosing the set of statements to transmit from one party to another.

The main point of semantic technologies is that any description of a resource can 
be decomposed into a collection of atomic statements.  Systems that reason about atomic 
statements as opposed to fixed frames tend to be more robust.  Semantic systems understand 
that different representations describe the same underlying resources.  They understand
the links between resources at a more fundamental level than the surface syntax of 
a particular representation.  Semantic systems can easily translate from one representation
to another because they decompose each representation into its atomic statements and 
then build those statements back up in new ways to form other representations.

Frequently, the backend storage system will use a frame-based knowledge representation. 
The storage system supports one and only one way to represent the
properties of a given object.  But this presents no problem to a semantic application.
The semantic application treats the persistent representation as just one of many possible
representations of the resource.  It can easily translate between this representation
of the data at rest and any other representation of the data in flight. 
