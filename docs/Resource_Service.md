---
layout: documentation
title: Resource Service
---

![Resource Service](/images/ResourceService.png)

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
