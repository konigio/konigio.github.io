---
layout: post
title: RDF in a nutshell
---

The Resource Description Framework (RDF) is a family of W3C specifications that provide a
general method for modeling information that is delivered through web resources using a 
variety of data serialization formats.

RDF is a kind of description logic.  Its underlying data model is a collection of `Statements`
of the form

~~~~
    <subject> <predicate> <object>
~~~~

Consider, for example, the following statements:

~~~
    Alice    has-mother       Susan  
    Alice    has-father       John  
    Alice    knows            Bob  
    Bob      has-mother       Martha
    Bob      has-father       Richard
    Bob      has-birthdate    "1994-08-07"
~~~   

Conceptually, a collection of statements forms a graph, where subjects and objects are nodes,
and the predicates are arrows connecting the nodes. Thus, the social network described above
can be rendered graphically:

![Social Network](/images/socialNetwork.png) 

The nodes within an RDF graph are called `Resources`.  Simple data values like strings, numbers,
dates, etc. are called `Literals`.  Nodes that represent people, places or things are called 
`Individuals`.

Each `Individual` may be identified by a URI value.  This way people and machines can 
lookup more information about the `Individual` by dereferencing the URI.  However, an `Individual`
is not *required* to have a URI.  If it has a URI, we say that it is a `NamedIndividual`.
Otherwise, we say it is an `AnonymousIndividual` or, in the vernacular, a *blank node*.  Blank nodes
cannot be accessed directly because they do not have an address.  They can be accessed only 
as properties of some other resource.

The predicate within an RDF `Statement` is the name for some `Property`.  Technically,
the name for an RDF `Property` is a URI, so you can lookup information about the Property.
To keep things simple in the narrative above, we've used short labels to name each `Property` 
instead of a URI.

We've been talking about using URI values as names for Individuals and Properties.  Technically, RDF allows
you to use [IRI](https://en.wikipedia.org/wiki/Internationalized_resource_identifier) values, which 
are an internationalized form of URI.


To summarize, we have the following glossary...

| Term                    |       Description                                                                                                                                                          |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Statement**           | A triple of the form `(subject, predicate, object)` which describes the relationship between some individual (the *subject*), and another resource (the *object*). |
| **Graph**               | A collection of `Statements`.                                                                                                                                           |
| **Individual**          | A node in a `Graph` that represents a person, place, or thing.  It can serve as the *subject* or *object* within a `Statement`                                        |
| **Literal**             | A node in a `Graph` that provides a simple data value like a number, date, boolean, string, etc. It can serve only as the *object* within a `Statement`               |
| **Resource**            | Any node within a `Graph`.  `Individuals` and `Literals` are special kinds of `Resources`.                                                                     |
| **NamedIndividual**     | An Individual that is named by an IRI.                                                                                                                                     |
| **AnonymousIndividual** | An Individual that is *not* named by an IRI                                                                                                                                |
| **Blank Node**          | Another word for `AnonymousIndividual`                                                                                                                            |  

  