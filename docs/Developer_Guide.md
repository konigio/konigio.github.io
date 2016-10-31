---
layout: documentation
title: Developer Guide
---

This article provides guidelines for developers who wish to contribute to the
Konig project.

# Project Repository
You can access source code from the [Konig project](https://github.com/konigio/konig)
on Github.  

# Project Artifacts
The Konig project publishes artifacts under the `io.konig` Maven group.
You can access the [published artifacts](http://search.maven.org/#search%7Cga%7C1%7Cio.konig)
in Maven Central.

The overall Konig project is broken down into the sub-projects that produce their
own artifacts.  Each sub-project is contained within a top-level folder having
the same name as its maven artifact ID.  

| Artifact ID     | Description                                                |
|-----------------|------------------------------------------------------------|
| konig-core      | A library for core classes (Graph, Vertex, Edge, etc.)     |
| konig-shacl     | A library for managing data shapes in accordance with the W3C SHACL standard |
| konig-schemagen | Tools for building code generators based on semantic models |
| konig-schemagen-maven-plugin | A Maven plugin for generating code based on semantic models |
| konig-ldp       | An implementation of the W3C Linked Data Platform          |
| konig-ldp-maven-plugin | A Maven plugin used to deploy resources to a linked data platform |

The Github repository includes other sub-projects, but these are the only ones
that build automatically as part of our continuous integration process.

# Branches for new Releases

We create a separate branch for each release of the Konig project.  The name for a release branch should match the following pattern:

```
   release/{major}.{minor}.{patch}
```

For example:

```
   release/2.0.0
```

The `major` version number is incremented whenever a non-backward
compatible change is introduced.

The `minor` version number is incremented whenever a new feature is
introduced.

The `patch` number is incremented whenever the release offers only
bug fixes (i.e. no new features).

# Feature Branches
When you are ready to work on a new feature or a bug fix, you should create your
own private branch with a name that matches the following pattern:

```
   {developer-id}/issue-{issueId}
```

For example:

```
   gmcfall/issue-14
```

The `developerId` value should be your public Github user ID.

The `issueId` value must match the numeric identifier for an Issue in the
Github issue tracker.

This means that you cannot start working until you have an issue in the issue
tracker.  If an issue does not exist, you can visit the
[issue tracker](https://github.com/konigio/konig/issues) and create one.

You can create a branch on your local machine and switch to this branch
using the following git commands:

```
$ git checkout -b [name_of_your_new_branch]   
```

Push the branch to github:

```
$ git push origin [name_of_your_new_branch]
```
# Publishing Changes


When you are ready to publish changes that resolve an issue follow these steps:

1.  Run a clean build: `mvn clean install`
2.  Commit your changes locally `git commit -m 'Fixed #14'`
3.  Push you changes to github: `git push`
4.  Create a pull request

The easiest way to create a pull request is through the Github UI.  Just visit your branch and then click the `New pull request` button.
