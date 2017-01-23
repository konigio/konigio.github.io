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
| konig-java      | A library of Java classes that support Konig generated POJO's | 
| konig-path-parser | An ANTLR parser Konig's extension of SPARQL Paths        |
| konig-core      | A library for core classes (Graph, Vertex, Edge, Shape, etc.)   |
| konig-transform | Tools for transforming data from one Shape to another      |
| konig-transform-maven-plugin | A Maven plugin which performs Shape transformations |
| konig-maven-diff | A Maven plugin which computes the difference between two graphs |
| konig-gae-datastore | A Runtime library for applications that use Google DataStore |
| konig-gae-generator | Tools for generating Google Datastore persisters and loaders |
| konig-schemagen | Tools for building code generators based on semantic models |
| konig-schemagen-maven-plugin | A Maven plugin for generating code based on semantic models |
| konig-ldp       | An implementation of the W3C Linked Data Platform          |
| konig-ldp-maven-plugin | A Maven plugin used to deploy resources to a linked data platform |
| konig-sql-core | Parser of SQL CREATE TABLE statements enriched with semantic markup |
| konig-sql      | Tools that interpret the semantic markup in SQL statements and generate RDF artifacts |
| konig-sql-maven-plugin | A Maven plugin that generates RDF artifacts based on semantic markup in SQL statements |

The Github repository includes other sub-projects, but these are the only ones
that build automatically as part of our continuous integration process.

# Eclipse Plugins
The following Eclipse plugins are recommended:

* ANTLR 4 SDK (if you will be designing ANTLR Parsers)
* Google App Engine Java SDK (if you will be developing GAE applications)
* Google App Engine Maven Integration (if you will be developing GAE applications)



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

# Create a new Google Appengine Project

Use the following procedure to create a new project for
Google Appengine.

1.  In Eclipse, choose New > Project...
2.  Select `Maven Project` and click `Next`
3.  Check the box for `Create a simple project` and click `Next`
4.  Fill out the form that provides the attributes for your pom file. Be sure to include the Parent Project attributes:
  - Parent Group Id: io.konig
  - Parent Artifact Id: konig-parent
5. Click `Finish`

Finally, add the following dependencies to your POM:

```
<dependencies>
		<dependency>
			<groupId>com.google.appengine</groupId>
			<artifactId>appengine-api-1.0-sdk</artifactId>
			<version>${appengine.version}</version>
		</dependency>
		<dependency>
			<groupId>com.google.appengine</groupId>
			<artifactId>appengine-testing</artifactId>
			<version>${appengine.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
			<scope>provided</scope>
		</dependency>
	</dependencies>

```
