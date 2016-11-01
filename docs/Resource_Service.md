---
layout: documentation
title: Resource Service
---

A Konig application provides a REST API for accessing resources in JSON format.
Under the hood, that REST API is powered by a `ResourceService` which offers
the following HTTP methods:

- GET
- POST
- DELETE


## POST Method

The following diagram shows the various classes used to implement the POST method.

![POST Method](/images/ResourceServicePost.png)

The `ResourceService` takes a `PostRequest` as input and persists a resource
in Google Datastore as a side-effect.

The `PostRequest` contains an `InputStream` for a JSON document.

The `ResourceService` uses the Jackson API to parse the `InputStream` contents
into an `ObjectNode`.  

The `ResourceService` uses a `JsonShapeReader` to transform the `ObjectNode` into
a `Vertex` within an RDF `Graph`.

The `JsonShapeReader` requires as input a description of the `Shape` of the data.

The `JsonShapeReader` uses a `ShapeManager` to discover the appropriate `Shape`.

The `JsonShapeReader` uses a `JsonDatatypeMapper` to discover the appropriate JSON
datatype (String, Number, Boolean) for RDF Literal values.

## GET Method

The following diagram shows the various classes used to implement the GET method.

![GET Method](/images/ResourceServiceGet.png)

The `ResourceService` takes a `GetRequest` as input and publishes a JSON object
to an output stream.

The `GetRequest` contains a `URI` reference to the resource that is to be published.

The `ResourceService` uses a `SemanticDatastore` to load the resource from Google Datastore.

The `SemanticDatastore` is a facade that wraps a `DatastoreService`.

The `SemanticDatastore` uses the `DatastoreService` to fetch an `Entity` from
persistent storage, and it then converts that `Entity` into a
`Vertex` that is contained within an in-memory RDF `Graph`.  

The `ResourceService` uses a `JsonShapeWriter` to transform the `Vertex` into
a JSON `ObjectNode`.

The `JsonShapeWriter` requires as input a description of the `Shape` that is to
be produced.

The `ResourceService` uses a `ShapeManager` to discover the appropriate `Shape`.
