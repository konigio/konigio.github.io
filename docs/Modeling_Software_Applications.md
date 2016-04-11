---
layout: documentation
title: Modeling Software Applications
---

Modeling software applications can be tricky.  The [Schema.org](http://schema.org/SoftwareApplication)
vocabulary defines some classes that provide a reasonable starting point for a semantic model. It 
includes the following hierarchy:

* Thing
	* CreativeWork
		* SoftwareApplication
			* MobileApplication
			* VideoGame
			* WebApplication
			
It's a bit surprising that there is no class for `DesktopApplication`, but one
can certainly add such a class.  The tricky bit, though, is deciding how to model particular
applications.  Consider for instance a application like gmail.  Should we model gmail as a subclass
of `SoftwareApplication` or an instance of `SoftwareApplication`?  It has incarnations
as both a `WebApplication` and a `MobileApplication`. 

We must choose between the following alternatives:

**Option 1: GMail as a Class**
```
:GMailApp a owl:Class ;
	rdfs:subClassOf schema:SoftwareApplication ;
	rdfs:comment "The class consisting of all Gmail applications"
	.
```

**Option 2: GMail as an Individual**
```
:GMailWeb rdf:type schema:WebApplication ;
	rdfs:comment """
		The production instance of the Gmail web application.
		There could be other instances, such as a test or
		staging instance.
	"""
```

These options are not mutually exclusive.  We can choose both.
In that case, we might write the following statement:

```
:GMailWeb rdf:type :GmailApp, schema:WebApplication .
```

Since there is only one production instance of the GMail web application, it is
reasonable to give it a name like `:GMailWeb`.

But there are many instances of the GMail mobile application. Every user has
his or her own private copy of that application installed on his or her device.  
Typically, it does not make sense to give a name to each individual instance of
a mobile app. These applications tend to be anonymous individuals.  Thus, we might 
have statements like the following:

```
[] rdf:type :GmailApp, schema:MobileApplication .
```

In English, these statements say, "There exists some thing that happens to be 
both a GMail application and a mobile application."  If we wanted to, we
could define a special class of all things that are both GMail applications and
mobile applications.  In that case we would write the following statements:

```
:GmailMobileApp rdf:type owl:Class ;
  rdfs:subClassOf :GmailApp, schema:MobileApplication .
  
[] rdf:type :GmailMobileApp .  
```
From these statements, a semantic reasoner may infer the earlier statements.  That is,
a semantic reasoner may infer

```
[] rdf:type :GmailApp, schema:MobileApplication .
```

To avoid the combinatorial explosion, it's usually best to just stick with 
the more fine-grained types than to introduce terms like `:GmailMobileApp` which represents
the intersection of two larger classes.  We shouldn't be too pedantic about this point, 
however. There are cases where intersection classes are useful.  Indeed, 
an intersection class allows you to define constraints that exist only at the intersection.
We'll see an example of this business case in the next section.

## Software Components

A given software application may have other applications as components.  For example,
Google Chat is a component of Google Mail.  But only in the Web version.  To complicate matters
further, Google Chat is available in the mobile version of Google Hangouts, but not in the web
version of Google Hangouts.

To model these relationships, we first need a way to express that a given application is part of
another application.  In Schema.org, we have the `hasPart` and `isPartOf` relationships.

Consider the following statements:
```

:GoogleApp rdf:type owl:Class ;
  rdfs:subClassOf schema:SoftwareApplication ;
  rdfs:comment """
    The class consisting of all Google applications
    including both web applications and mobile applications
  """ .

:GMailApp a owl:Class ;
	rdfs:subClassOf :GoogleApp ;
	rdfs:comment """
    The class consisting of all Gmail applications 
    on the web or on mobile devices
  """ .
  
:GChatApp rdf:type owl:Class ;
  rdfs:subClassOf :GoogleApp ;
  rdfs:comment "The class of all Google Chat applications"
  .

:GChatWebApp rdf:type owl:Class ;
  rdfs:subClassOf :GChatApp, schema:WebApplication ;
  rdfs:comment """
    The intersection of Google Chat applications 
    and web applications.
  """ .
  
:HangoutApp rdf:type owl:Class ;
  rdfs:subClassOf :GoogleApp ;
  rdfs:comment """
    The class of all Google Hangout applications
    on the web or on mobile devices
  """ .
  
:HangoutMobileApp rdf:type owl:Class ;
  rdfs:subClassOf :HangoutApp, schema:MobileApplication ;
  rdfs:comment """
    The intersection of Google Hangout applications
    and mobile applications
  """ .

:HangoutMobileApp rdfs:subClassOf [
  a owl:Restriction ;
  owl:onProperty schema:hasPart ;
  owl:someValuesFrom :GChatMobileApp
] .

:GMailWebApp rdfs:subClassOf [
  a owl:Restriction ;
  owl:onProperty schema:hasPart ;
  owl:someValuesFrom :GChatWebApp
] .

:GMailWeb rdf:type :GMail, schema:WebApplication ;
  rdfs:comment """
    The production instance of the GMail 
    web application
  """ ;
  schema:hasPart :GChatWeb .
  
:GChatWeb rdf:type :GChatWebApp ;
  rdfs:comment """
    The production instance of the Google Chat web 
    application.
  """ .
```

Notice the restriction on `:HangoutMobileApp`.  In plain English, it asserts
that every instance of the Google Hangout mobile application has an instance of the
Google Chat application as a part.

Similarly, the restriction on `:GMailWebApp` asserts that every instance of
the GMail web application has an instance of Google Chat application as a part.

In particular, the production instance `:GMailWeb` has `:GChatWeb` as 
a part.

	


   
