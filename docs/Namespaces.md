---
layout: documentation
title: Resource Types
---

Semantic models allow you to define class hierarchies.  
Here's a partial listing of the class hierarchy at schema.org:

* Thing
	* Action
	* CreativeWork
		* Article
			* NewsArticle
			* Report
			* ScholarlyArtical
			* SocialMediaPosting
		* Blog
		* Book
		* SoftwareApplication
		  * MobileApplication
		  * VideoGame
		  * WebApplication
		  
The [full hierarchy](http://schema.org/docs/full.html) is available from schema.org.		  

Class hierarchies are defined formally in OWL using statements like

```
	schema:Action rdfs:subClassOf schema:Thing .
	schema:CreativeWork rdfs:subClassOf schema:Thing .
	schema:Article rdfs:subClassOf schema:CreativeWork .
	schema:NewsArticle rdfs:subClassOf schema:Article .
	etc.
```

The `rdf:type` predicate expresses that a given resource is an instance of some class. 
Thus, one might assert the following statement:

```
	dbpedia:Tetris rdf:type schema:VideoGame .
```

Given the class hierarchy, this statement implies that `Tetris` is also a `SoftwareApplication`, 
a `CreativeWork`, and a `Thing`.  Using semantic reasoning, we can infer the following 
additional statements:

```
	dbpedia:Tetris rdf:type schema:SoftwareApplication, schema:CreativeWork, schema:Thing .
```

When designing a data contract, one must decide how to represent type information.  
Broadly speaking, there are two options:

1.  The publisher is responsible for semantic reasoning and should report all known types for a given resource.
2.  The consumer is responsible for semantic reasoning and may infer additional types from the class hierarchy while processing the description of a resource.




  