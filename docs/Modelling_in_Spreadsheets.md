---
layout: documentation
title: Modelling in Spreadsheets
---

This article explains how you can design a semantic model in a spreadsheet.
The Konig toolkit includes a Maven plugin that will convert the information in
a spreadsheet into a collection of OWL and SHACL statements.

This capability makes it easy to develop semantic models without having to
understand the syntax of the OWL and SHACL standards.

# File format
The Maven plugin requires the spreadsheet to be stored in Microsoft Excel
Open XML Format: `*.xlsx`

 # Workbook Overview
A workbook may contain sheets that describe the following kinds of entities
within a semantic model:

* `Ontology`
* `Individual`
* `PropertyConstraint`
* `Class`
* `Property`
* `Shape`

In most cases, you won't need to define `Class`, `Property` and `Shape` entities
explicitly.  They can be derived from other information in the workbook.

The sections below give detailed instructions for defining these entities in
a spreadsheet.

# Details

## Describing Ontologies

An ontology provides a namespace within which the elements of your model are
scoped.  

Consider, for example, the [Person](http://schema.org/Person) class defined by
the Schema.org ontology.

The fully-qualified identifier for this class is:

```
    http://schema.org/Person
```

The namespace is `http://schema.org/` and `Person` is a local name within that
namespace.

It is a common practice to map each namespace URI to a short name that can be
used as the prefix in compact URIs.  See [CURIE](https://en.wikipedia.org/wiki/CURIE) for
a discussion of this practice.

As an example, suppose we define "schema" as the namespace prefix for
`http://schema.org/`.  In that case, we can shorten the fully-qualified URI
`http://schema.org/Person` to the CURIE `schema:Person`.

An Ontology sheet defines the namespaces used in your model.

It may contain the following columns.

| Column Name     | Description                                                |
|-----------------|------------------------------------------------------------|
| Ontology Name   | A human-friendly name for the ontology, suitable for display in a user interface|
| Comment         | Some plain-text that describes the purpose of the ontology       |
| Namespace URI   | A URI that uniquely identifies the namespace               |
| Prefix          | A short string that can be used as a short alias for the namespace. |

## Describing Individual Terms

Individuals are instances of a given class.  Individuals are used to define
reference data, i.e. terms from a controlled vocabulary.

A sheet containing reference data may have the following columns:

| Column Name     | Description                                                |
|-----------------|------------------------------------------------------------|
| Individual Name | A human-friendly name for the term in the controlled vocabulary |
| Comment         | Some plain-text that provides a description of the term    |
| Individual Id   | A fully-qualified URI or CURIE that identifies the term    |
| Individual Type | The type of term.  This is either a fully-qualified URI or a CURIE for a `Class`. |

In addition to these standard column names, you can add columns for custom properties.
The column heading is a SPARQL path.

For example, suppose you want to define an icon for each term in a given controlled
vocabulary.  You could add a column named `/schema:image`.

## Defining Property Constraints

The bulk of a semantic model is contained in the Property Constraints which define
the set of properties that are available in a each data shape.

A Property Constraints sheet may have the following columns

| Column Name     | Description                                                |
|-----------------|------------------------------------------------------------|
| Shape Id        | The identifier for the `Shape` that contains the property.  This identifier can be a fully-qualified URI or a CURIE. |
| Property Id     | The identifier for the property that is being constrained. This identifier can be a fully-qualified URI or a CURIE. |
| Comment         | A plain-text string that provides a description of the property |
| Value Type      | The type of values that are expected for the property.  The value can be a simple datatype from the XML Schema namespace, or the identifier for a `Shape`. |
| Min Count       | An integer specifying the minimum number of values of the property.  An optional property would have the Min Count equal to zero.  If a value is required, then Min Count will be greater than zero. |
| Max Count       | An integer specifying the maximum number of values of the property, or the string "unbounded" if there is no limit on the number of values allowed. Single-valued properties will set Max Count to `1`|
| Value Class     | The identifier for the class referenced by this property.  This value is meaningful only if the property is a URI reference, in which case the `Value Type` should be set to `xsd:anyURI`. |
