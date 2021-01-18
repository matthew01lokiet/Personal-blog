---
title: 'Hibernate - Theoretical Overview'
date: 2021-01-17 00:00:00
description: Theory standing behind the most popular JPA implementation - Hibernate. 
featured_image: '/images/posts/hibernateOverview/hibernate_overview.png'
tags: hibernate java programming database
---

## JPA Specification
Not so long ago, the only way to connect the existing java app to a given database instance
was by using <span class="inline_text">JDBC</span>.

It worked, but at the same time, its usage generated a lot of boilerplate code, where
you had to wrap <b>fetched table records</b> in <b>java objects</b> by yourself and embed <b>SQL queries</b> directly
into your method calls to issue even simple <b>CRUD</b> operations.

You can check my post to see how <span class="inline_text">JDBC</span> works in practise: <a href="https://www.javaendlessjourney.com/blog/jdbc-introduction"> link </a>.

But then couple of smart people decided that it can't be it; there must be a simpler way to create
data flow without redundancy connected to writing the same code again and again.

They've come up with the specification known as <span class="inline_text">JPA</span>, which states how the <span class="inline_text">ORM</span> mechanism should be implemented:

* <b><span style="color: black;">JPA</span></b> - <b>Java Persistence API</b> -> official standard specification used to implement ORM in the Java programming environment. 
* <b><span style="color: black;">ORM</span></b> - <b>Object-Relational Mapping</b> -> specific way in which we can convert data between relational and objective type systems.

<span class="inline_text">ORM</span> mapping example:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/hibernateOverview/orm_mechanism.png">
</div>

## Hibernate - the most popular JPA implementation
So at this point we know that:
* <b>JPA</b> is just a specification; the template for how should be the <b>ORM</b> mechanism implemented.
* <b>ORM</b> on his own is only a brief overview of the mechanism, in our case, focused on translating database table row into java object.

We are missing something - we have a lot of specifications, but we didn't hear about any implementation.

And this is when <b><span style="color: black;">Hibernate</span></b> arrives.

It's the framework that provides the implementation for <b>JPA</b> specification by extensive usage of <b>ORM</b> mechanism to translate <b>SQL</b> table rows into 
compatible java objects!

Also, it easies the usage of fundamental queries on the database by having implemented <b>CRUD</b> operations as functions that can be used from start without the explicit need to write your own code.

## Hibernate - how it works?
<span class="inline_text">Hibernate</span> internally consist of two main objects which we will use:

<b><span style="color: black;">Session Factory</span></b>:
* object created once for the whole app.
* it reads <b>Hibernate</b> config file, that have been set up by us.
* we can retrieve <b>Session</b> object on demand using <b>Session Factory</b>.

<b><span style="color: black;">Session</span></b>:
* object, which wraps around the connection to the database.
* used to issue queries and build-in <b>CRUD</b> operations.
* retrieved on request from <b>Session Factory</b>.

Also we should tackle the topic of <span class="inline_text">Persistent Context</span>:
* <b><span style="color: black;">Persistent Context</span></b> - staging area between database and java app, which manages entity lifecycle.

But how this "<b>staging area</b>" works?

If you have had any previous experience with distributed version-control system known as "<b>GIT</b>", you will notice many similarities.

To get a better overview, let's take a look at this simple chart:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/hibernateOverview/persistent_context.png">
</div>

The main point you should remember is when you create, retrieve, update, delete, or make any other operation on the database,
you won't notice any changes, until you <b>commit</b> them!

This gives us two advantages:
* changes to the database won't appear instantly, so we are still able to add or drop some operations even after issuing. You can think about it as a "<b>Safety Buffer</b>".
* instead of committing operations one at a time, even though you have many of them, you can stage and commit all of the operations at once!

## Entity Lifecycle
Another question arises - what's going on inside this "<b>staging area</b>" and how <span class="inline_text">Persistent Context</span> takes care of the entity lifecycle management?

Furthermore - what does "<b>entity</b>" even mean?

<b><span style="color: black;">Entity</span></b> - Plain Old Java Object used as a data carry object, which serves us as the wrapper for the fetched record from the database table.

In <span class="inline_text">Hibernate</span> staging area, we can differentiate 4 entity lifecycle <b>states</b>:
* <b><span style="color: black;">Transient</span></b> - entity object created, but not staged for any changes. Persistent Context doesn't even know about its existence.
* <b><span style="color: black;">Managed (Persisted)</span></b> - entity object staged for the changes; currently put into Persistent Context and managed by it.
* <b><span style="color: black;">Detached</span></b> - entity object not longer recognized by Persistent Context, but managed by it in the past.
* <b><span style="color: black;">Deleted (Removed)</span></b> - entity object staged for permanent deletion.

## Query Operation Types
When working with <span class="inline_text">Hibernate</span>, we have mainly 2 ways in which we can create queries:
* <b><span style="color: black;">Build-in CRUD operations</span></b> - mentioned earlier; we are able to issue them by calling the functions like "<b>.save()</b>" or "<b>.delete()</b>" on <b>Session</b> object.
* <b><span style="color: black;">HQL Syntax</span></b> - SQL dialect focused not on the database table name and properties, but on the entity object class and fields.
Allows us to build custom queries.

This way we don't have to write additional code for fundamental operations, unless there is an explicit need (more complicated queries), in which case we allowed
to create our custom piece of code with the <b>HQL</b> help.

## What's next
As we know, theory and practice are two different things, so we won’t stop. In the next posts, I will show you how to add, configure, and use <span class="inline_text">Hibernate</span> in your app.

Also, we will design a simple database for managing <b>Truck Companies</b> transport orders, which will serve us as a testing example for what we are able to achieve with the <span class="inline_text">Hibernate</span> functionalities.

See you next time!


