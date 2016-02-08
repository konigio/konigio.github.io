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
| `https://www.konig.io/resource/Sergey_Bring.json` | The address where you can get a description of Sergey Brin in JSON format |

Here's the important point: each *resource* is identified by a particular URI, and each
*description* of a resource is identified by a different URI.

### Data Shapes
You may want to offer distinct *views* of a given resource.
For instance, you might want a *financial* view of a person that provides salary and bonus information and a
*social* view that provides information about the person's social network.

Thus, we might have the following additional URIs.


| URI                                                                                          | Comment                                                                 |
|----------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| `https://www.konig.io/resource/Sergey_Brin.social.schema.person.json`     | The address where you can get information about Sergey's social network |
| `https://www.konig.io/resource/Sergey_Brin.financial.schema.person.json` | The address where you can get information about Sergey's compensation   |

The social record might look something like this:

<div class="example">
  <div class="example-title">Final Resource</div>
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

The financial record might look something like this:

<div class="example">
  <div class="example-title">Final Resource</div>
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



