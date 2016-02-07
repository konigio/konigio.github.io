---
layout: post
title: Resource Descriptions
---

A *Resource* is anything that can have a URI.  It can be a digital asset or an entity that
represents something in the real world such as a person, place, event, idea, etc. 

It is important to distinguish between a resource and a *description* of the resource. A
given resource can have many different descriptions in different formats. 

Thus, we might have the following URI values.

| URI                                             | Comment                                                                   |
|-------------------------------------------------|---------------------------------------------------------------------------|
| https://www.konig.io/resource/Sergey_Brin       | The URI for a resource that represents one of the co-founders of Google   |
| https://www.konig.io/resource/Sergey_Brin.xml   | The address where you can get a description of Sergey Brin in XML format  |
| https://www.konig.io/resource/Sergey_Bring.json | The address where you can get a description of Sergey Brin in JSON format |

Here's the important point: each *resource* is identified by a particular URI, and each
*description* of a resource is identified by a different URI.

Moreover, it is possible to have more than one description in a given format.  You might want to
support different *views* of a resource that include different sets of properties.  



