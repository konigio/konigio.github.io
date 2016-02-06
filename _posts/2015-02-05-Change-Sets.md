---
layout: post
title: Change Sets
---

A `ChangeSet` describes the delta between two versions of a resource.  It contains a set of statements 
that are added to and a set of statements that are removed from one version of the resource to produce the 
subsequent version.  The resource modified by the `ChangeSet` could be a discrete individual (Person, 
Place, Thing, etc.) or a graph of individuals.

```javascript
	{
		"@id" : "http://dbpedia.org/resource/Theodore_Roosevelt",
		"givenName" : "Theodore",
		"familyName" : "Roosevelt"
	}
```

<div class="example">
	<div class="example-title">Original Resource</div>

</div>

