---
layout: documentation
title: The Case for Semantic Modeling
---

You might be wondering why we need a semantic model at all.  Wouldn't it be easier to
just work with Avro schema and skip the semantic model altogether?  

The short answer is that Avro schemas are brittle because they express a physical model for data on the wire in 
one particular format.  But a given resource can have more than one description; there may be multiple
*views* of a given resource.  While Avro supports schema evolution to some extent, it does not offer a
mechanism for stitching together alternative views of a resource.  

Let's look at a concrete example.  Suppose you decide to use the 
[W3C Activity Streams Vocabulary](https://www.w3.org/TR/activitystreams-vocabulary/) to report
social interactions.  For instance, whenever someone "likes" a blog post, you might publish a
record like the one shown below, where `endTime` is the time of the event expressed in
milliseconds since the beginning of the epoch (00:00:00 January 1, 1970).

**Sample Activity Record: Version 1**

```json
  {
    "type" : "as:Like",
    "actor" : "http://example.com/person/alice",
    "object" : "http://example.com/person/bob/post/5",
    "endTime" : 1461753982
  }
```

Here's the schema for this record expressed in [Avro IDL](https://avro.apache.org/docs/1.8.0/idl.html)
syntax:

**Schema for Activity Records: Version 1**

```java
@namespace("com.example.analytics.v1")
protocol Analytics {

  record Like {
    string type;
    string actor;
    string object;
    long endTime;
  }
}
```

Given a stream of such records you can compute some interesting metrics including:

*  The number of times that a given social media posting has been liked
*  The number of times that a given actor has liked something

You might produce reports that aggregate these metrics by day, week and month.  The product manager is delighted with the result.  

A couple months later, she asks that you slice the metrics based on the educational organization to
which the actor belongs.

To support this requirement, you decide to enrich the activity record so that it includes information
about the actor's affiliation with an educational organization as shown below.

**Sample Activity Record: Version 2**

```json

  {
    "type" : "as:Like",
    "actor" : {
      "id" : "http://example.com/person/alice",
      "memberOf" : ["http://example.com/org/stanford"]
    },
    "object" : "http://example.com/person/bob/post/5",
    "endTime" : 1461753982
  }

```

This change implies a new schema, shown below.

**Schema for Activity Records: Version 2**


```java
@namespace("com.example.analytics.v2")
protocol Analytics {

  record Like {
    string type;
    Person actor;
    string object;
    long endTime;
  }
  
  record Person {
    string id;
    array<string> memberOf;
  }
}

```

Suppose there are multiple systems submitting activity streams to your analytics
engine, and they will migrate to the new schema at different times.
Since the new schema (version 2) is not backward compatible with the original 
one (version 1), you cannot take advantage of Avro's schema evolution 
features. You need to support both versions simultaneously.  If you receive an Activity record
based on the original schema, you must create a new record enriched with the 
additional information about the user in accordance with the new schema.

By design, the `actor` field in version 1
is the URL for a `Person` resource, and you can get more information about the Person
by dereferencing the URL.  Whenever you receive a message that conforms to version 1 of the
schema, you'll need to get a complete description of the `Person` from the system of record
by issuing a GET request like this:

```
GET /person/alice HTTP/1.1
HOST example.com
```

The system of record might return detailed information about the `Person` like this:

```json
{
  "id" : "http://example.com/person/alice",
  "givenName" : "Alice",
  "familyName" : "Jones",
  "email" : "ajones@example.com",
  "telephone" : "1-555-123-4567",
  "memberOf" : ["http://example.com/org/stanford"]
}
```

This record describes Alice according to yet another schema, shown below.

```java
@namespace("com.example.master.v1")
protocol MasterData {

  record Person {
    string id;
    string givenName;
    string familyName;
    string email;
    string telephone;
    array<string> memberOf;
  }
}
```

It's fairly straight forward to write some code that transforms a
`com.example.master.v1.Person` record into a 
`com.example.analytics.v2.Person` record so that you can embed it into a
`com.example.analytics.v2.Like` record.  

More accurately, it straight forward for a human engineer to code the transform.  But
it is nearly impossible to automate a code generator because there is
no way for a machine to know how the bits of data fit together.  

We humans know the following statements are true:

1.  The `actor` field of `com.example.analytics.v1.Like` is the URI for a `Person` entity
2.  The complete description of a `Person` entity is given by `com.example.master.v1.Person`
3.  The record described by `com.example.analytics.v2.Person` is a subset of the complete `Person` entity.

It is impossible to deduce this information from the Avro schemas alone.  If you want to automate the ETL process
you must add this information somehow.  That's exactly what the semantic model does.  It adds information about 
the relationships between different physical manifestations of a logical data model.  

We should also observe that an SDK generated from an Avro schema alone will be 
brittle because it reflects one particular physical
manifestation instead of the underlying logical model.

Let's explore this point in some detail.

Here's a skeleton of the generated POJO for `com.example.analytics.v1.Like`. 

```java
package com.example.analytics.v1;

public class Like {
  
  public void setType(String type) {}
  public String getType() {}
  
  public void setActor(String actor) {}
  public String getActor() {}
  
  public void setObject(String object) {}
  public String getObject() {}
  
  public void setEndTime(long endTime) {}
  public long getEndTime() {}

}
``` 

This POJO has some undesirable features.  The relationships to other
entities are encoded as `String` references.  Based on the schema alone, there is
no way to know that these fields actually represent references to other entities
let alone what kind of entities.

It would be better to build an API based on the logical model.  Such an API 
anticipates that we might need to enrich the activity data with 
additional properties belonging to any of the related entities.  That is to say 
it would be good to *start* with an API that uses value objects rather than URI
references. With sufficient foresight, we would have started with
a POJO that looks like this:


```java
package com.example.as;

public class Like {
  
  public URI getType() {}
  
  public void setActor(Person actor) {}
  public Person getActor() {}
  
  public void setObject(SocialMediaPosting object) {}
  public SocialMediaPosting getObject() {}
  
  public void setEndTime(Date endTime) {}
  public Date getEndTime() {}

}
```

Now the semantics are clear, and we have a solid foundation to build upon.  We can enrich the 
data about related entities to our hearts content. There is no way to *start* with a POJO
like this if you are generating from a physical model like Avro.  However, if you are building
systems based on a semantic model, it is easy to begin with a semantically rich API right
out of the gate. By starting with a semantically rich API, you minimize the code changes that
will be required by your clients as the solution evolves.


