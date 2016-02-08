---
layout: post
title: Resource Descriptions
---

A *Resource* is anything that can have a URI.  It can be a digital asset or an entity that
represents something in the real world such as a person, place, event, idea, etc. 

It is important to distinguish between a resource and a *description* of the resource. A
given resource can have many different descriptions. 

For instance, we might have the following URI values.


| URI                                                             | Comment                                                                   |
|-----------------------------------------------------------------|---------------------------------------------------------------------------|
| `https://www.konig.io/resource/Sergey_Brin`         | The URI for a resource that represents one of the co-founders of Google   |
| `https://www.konig.io/resource/Sergey_Brin.xml`    | The address where you can get a description of Sergey Brin in XML format  |
| `https://www.konig.io/resource/Sergey_Brin.json`  | The address where you can get a description of Sergey Brin in JSON format |

Here's the important point: each *resource* is identified by a particular URI, and each
*description* of a resource is identified by a different URI.

### Resource Views
A server may offer distinct *views* of a given resource.  Each view provides a description of the resource from a different perspective.
For instance, the server might offer a *financial* view of a person that provides salary and bonus information and a
*social* view that provides information about the person's social network. 

Thus, we might have the following additional URIs.


| URI                                                                                          | Comment                                                                 |
|----------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| `https://www.konig.io/resource/Sergey_Brin/social.schema.person.json`     | The address where you can get information about Sergey's social network |
| `https://www.konig.io/resource/Sergey_Brin/financial.schema.person.json` | The address where you can get information about Sergey's compensation   |

The social view might look something like this:

<div class="example">
  <div class="example-title">Social View</div>
  <pre class="json">
  {
    "@context" : "http://www.konig.io/shape/social/schema/person/ctx",
    "@id" : "https://www.konig.io/resource/Sergey_Brin",
    "givenName" : "Sergey",
    "familyName" : "Brin",
    "knows" : [
      "https://www.konig.io/resource/Larry_Page",
      "https://www.konig.io/resource/Sundar_Pichai",
      "https://www.konig.io/resource/Eric_Schmidt"
    ]
  }
  </pre>
</div>

The financial view might look something like this:

<div class="example">
  <div class="example-title">Financial View</div>
  <pre class="json">
  {
    "@context" : "http://www.konig.io/shape/financial/schema/person/ctx",
    "@id" : "https://www.konig.io/resource/Sergey_Brin",
    "givenName" : "Sergey",
    "familyName" : "Brin",
    "baseSalary" : "$1",
    "stockOptions" : 0
  }
  </pre>
</div>

The structure of a given view can be specified formally by a [data shape](https://www.w3.org/TR/shacl/).  
We'll have more to say about data shapes later.  For now, just think of a `Shape` as a schema
that prescribes the elements that appear within a given view.

There are many ways that a web site manager might choose to publish views of a resource.
The Konig platform supports three design patterns

1. **URL path parameters**
  ```
    GET /resource/Sergey_Brin/social.schema.person.json
  ```
2. **Query parameters**
  ```
    GET /resource/Sergey_Brin?shape=social.schema.person
  ```
3. **Content Negotiation**
  ```
    GET /resource/Sergey_Brin
    Accept: application/vnd.konig.social.schema.person+json
  ```

We discuss these design patterns in some detail below.


### Using URL path parameters to request a view

In the Konig platform, the URL for a view based on a given `Shape`



