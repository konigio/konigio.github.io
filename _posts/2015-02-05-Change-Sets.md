---
layout: post
title: Change Sets
---

A `ChangeSet` is a completely general mechanism for describing the delta between two versions of a graph.  
You move from one version to another by applying the `ChangeSet`, and you undo the changes by rolling it back.

The `ChangeSet` entity is inspired by a similar concept created by Sam Tunnicliffe and Ian Davis with the same name.
But there are some important differences.  The Tunnicliffe and Davis ChangeSet defines a set of reified `rdf:Statement`
resources that are added to the initial graph and a set of reified `rdf:Statement` resources that are removed from the
initial graph to produce the subsequent version.  

Instead of using reified statements, our `ChangeSet` bundles the additions into a discrete `Graph` and the removals 
into another `Graph`.  