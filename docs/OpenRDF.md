---
layout: documentation
title: OpenRDF
---

The Konig platform builds on the
[OpenRDF Sesame](https://en.wikipedia.org/wiki/Sesame_(framework))
framework for querying and analyzing RDF data.

In May 2016, Sesame officially forked into an Eclipse project called [RDF4J](http://rdf4j.org/).  
The Konig platform will eventually migrate to  RDF4J.
But we want to operate within Google App Engine, which requires Java 7.
Since RDF4J requires Java 8, the Konig platform will continue to use OpenRDF
Sesame (version 2.8.7) until GAE provides full support for Java 8.

Unfortunately, the OpenRDF documentation is no longer available. So the best
way to learn about OpenRDF is by reading the RDF4J documentation.  The APIs are
the same.  Only the names of the packages have changed.

Here's a list of documentation artifacts that are relevant to the Konig platform:

* [RDF Building Blocks: IRIs, literals, blank nodes and statements](http://docs.rdf4j.org/programming/#_rdf_building_blocks_iris_literals_blank_nodes_and_statements)
* [Parsing and Writing RDF with Rio](http://docs.rdf4j.org/programming/#_parsing_and_writing_rdf_with_rio)
* [Javadocs](http://archive.rdf4j.org/javadoc/sesame-2.8.7/)


The Konig Platform uses a fairly small subset of the OpenRDF Sesame classes
directly.  Here's the full list:

* org.openrdf.model.BNode
* org.openrdf.model.Literal
* org.openrdf.model.Namespace
* org.openrdf.model.Resource
* org.openrdf.model.Statement
* org.openrdf.model.URI
* org.openrdf.model.Value
* org.openrdf.model.ValueFactory
* org.openrdf.model.impl.BNodeImpl
* org.openrdf.model.impl.BooleanLiteralImpl
* org.openrdf.model.impl.ContextStatementImpl
* org.openrdf.model.impl.LiteralImpl
* org.openrdf.model.impl.NamespaceImpl
* org.openrdf.model.impl.StatementImpl
* org.openrdf.model.impl.URIImpl
* org.openrdf.model.impl.ValueFactoryImpl
* org.openrdf.vocabulary.DC
* org.openrdf.vocabulary.FOAF
* org.openrdf.vocabulary.OWL
* org.openrdf.vocabulary.RDF
* org.openrdf.vocabulary.RDFS
* org.openrdf.vocabulary.SKOS
* org.openrdf.vocabulary.XMLSchema
* org.openrdf.rio.RDFFormat
* org.openrdf.rio.RDFHandler
* org.openrdf.rio.RDFHandlerException
* org.openrdf.rio.RDFParseException
* org.openrdf.rio.RDFParser
* org.openrdf.rio.RDFWriter
* org.openrdf.rio.helpers.BasicWriterSettings
* org.openrdf.rio.helpers.RDFHandlerBase
* org.openrdf.rio.helpers.RDFParserBase
* org.openrdf.rio.turtle.TurtleParser
* org.openrdf.rio.turtle.TurtleParserFactory
* org.openrdf.rio.turtle.TurtleWriter
* org.openrdf.rio.turtle.TurtleWriterFactory
