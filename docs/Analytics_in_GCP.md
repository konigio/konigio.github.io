---
layout: documentation
title: Analytics in GCP
---

The Konig toolkit can help you build analytics solutions in Google Cloud
Platform by assisting with the following tasks:

* Build a Semantic Model
* Publish Documentation
* Define BigQuery Tables
* Load Data into your Warehouse
* Define your KPIs
* Compute KPIs
* Provide a Data Services API

In the sections below, we summarize each task and explain how Konig can
help you complete the task.  Some of the Konig features are forward-looking.
Sections marked with an asterisk describe features that have not yet been
implemented.

## Build a Semantic Model
If you are going to build an analytics solution, you first need to understand the
data that you are working with.  A semantic model defines the entities within
your domain.  It specifies the properties
of each entity and the relationships to other entities.  

## Publish a Data Dictionary
A semantic model is nice, but it doesn't do you any good if it developers and
analysts cannot easily digest the information contained in the model.  Konig
provides a way for you to publish a user friendly data dictionary directly
from the semantic model.

## Generate BigQuery Tables
BigQuery is Google's cloud-based data warehouse solution.  When computing key
performance indicators, you will often need to query data within the warehouse.
Konig provides tools to generate BigQuery table definitions directly from
your semantic model.

## Load Data into your Warehouse
Konig provides tools that make it easy to load your data into the warehouse.  
There are two main ways to load data:

* Batch Load
* Data Streaming

## Define your KPIs
A Key Performance Indicator (KPI) is the metric that you want to measure.
Most people tend to define Key Performance indicators in a natural language like
English.  If specified at all, the algorithm that computes a given KPI is often
expressed only in natural language like English or in pseudo-code. In that case,
you need an engineer to translate the specification into functioning
software.  Konig offers an alternative approach.  In many cases, you can create
a machine-readable specification for your KPIs, and Konig will generate the
processing pipeline that computes your metrics as described below.

## Compute KPIs
Given a formal specification for a KPI, Konig can generate the software that
computes those metrics.  There are two different ways to compute metrics:

1. Generate a query that computes KPIs in a batch fashion.
2. Generate a Dataflow processing pipeline that computes KPIs in near real-time.
