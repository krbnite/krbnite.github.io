---
title: Data Modeling with Entity Relationship Diagrams
layout: post
---

There are a lot of options out there for software to design these diagrams in, and I'll get
to using MySQL Workbench in another post (or who knows: maybe at the end of this post).  But right
now, I want to emphasize the good ol' fashioned pen-and-paper approach (pen, b/c fuck pencils).

At work, we have wearable devices.  Each device has sensors.  Devices can be categorized
into classes -- wrist watches, chest straps, socks, and so on.  The same can be said for sensors: accelerometers,
magnetometers, gyroscopes, and more!  For example, a Fitbit Ionic is a wrist watch that has an accelerometer.  Each
sensor is associated with properties, such as sampling frequency, signal resolution, and dynamic range.  Sometimes 
the raw sensor data is accessible via an API.  Sometimes USB. Sometimes not at all.  Sometimes a composite data
stream is provided instead of, or in addition to, the raw data streams.  

A knowledge base like this can become quite complex, and we need a good knowledge map lest we get lost!  Entity
relationship diagrams (ERDs) are a way to do this.  What's nice is that, following a few simple rules, one
can use ERDs to construct fully functional, normalized databases. (You might ask: What is a normalized database? 
And I might say: that's a good question -- hold it for later.)

# An Example: Automated Geophysical Observatories
To avoid going into too much work detail, let's take an example from a past life -- let's model the instrumentation
associated with the Automated Geophysical Observatories (AGOs) down in Antarctica.  

## Write Something
What are the things you want to model?  What are the properties of those things
that you want to keep track of?  How does one thing relate to another?

If those questions sound vague, then good!  That's the point: before you can design a data 
model, you must capture what it is you want to model.  One way to do this is to write a few
paragraphs that describe the things, the situations, the use cases.  From this, one can
extract a "candidate entity list" and begin diagramming!  

We want to design a database that keeps track of what **instruments** are onboard each **AGO**,
during what **times** those instruments have typically have data (e.g., all year long, austral winter,
etc), and what **phenomena** (physical processes and events) the instrument can monitor via its 
**raw data stream** and/or **derivations** from that data.

From this, we can extract a candidate entity list:
* instruments
* AGOs
* times
* phenomena
* raw data stream
* derived data streams / data products

The keyword here is "candidate" -- these might be our entities of interest, or maybe only
a few them will be.  Importantly, our juices are flowing (ew!).  This will be an iterative 
process.  The next step is to draw some boxes on a piece of paper and decide whether
the relationships between these things are one-to-one (one human has one soul), one-to-many
(one human has many souls), many-to-one (many humans share one soul), or many-to-many 
(many humans share many souls).  Depending on your metaphysical philosophy, one of those
choices might be the "correct" data model.  Back in real life, we have AGOs, instruments, and
physical phenomena -- let's start drawing!

<img src="/images/ago-db-erd-1.jpg" width="500">

Wow, that's messy! And purposely so: you need to get your hands dirty as quickly and as
often as possible.  The above diagram does not even fully follow ERD principles... I've included
little (\*) symbols intersecting association lines, which means nothing more than, "I need to
include this relationship somehow, but can't figure it out at the moment."  And that's fine!  If you
don't feel overwhelmed a little bit at first, then ... want to help me do some data modeling?!

One of the principles of ERD modeling is to resolve any one-to-one or many-to-many relationships.  

The idea is, any one-to-one relationship can be a part of the same table.  An exception might be when we want
to secure certain data -- e.g., we might keep a social security number in its own restricted-access table, and
map to it with a less sensitive ID.

As for many-to-many, the ERDs will eventually
be mapped into actual RDBMS tables, and many-to-many relationships will not make for a normalized database. (Again,
you might ask: what is a normalized database? Great question! Ask me again later.) 

We see a one-to-one relationship between "instrument subclass" and "instrument class."  Basically, these tables
are trying to reflect that the specific searchcoil magnetometer onboard an AGO is of subclass "searchcoil magnetomter,"
which is of class "magnetometer."  It might have been better to word it as "class" and "category."  The point is,
each specific instrument maps to one "category" (imaging riometer), which maps to one class (riometer).  

As for the many-to-many relationships, some are easily resolvable with a "join table," which I usually like to call
a "bridge table" or "relationship table" in my head.  I like to think in terms of dictionaries: we want an AGO dictionary,
which uniquely identifies the AGOs; we want an instrument dictionary, which uniquely identifies what instruments might be
onboard an AGO; and we want an "interaction table" (that's another term I like to use) that records how the entities in
these dictionaries interact (or relate to each other, thus "relationship table").  

<img src="/images/ago-db-erd-2.jpg" width="500">

Upon iteration, I think that (maybe) the "instrument" table shouldn't map directly to the "phenomena" table.  For
example, the fluxgate magnetometer passively records magnetic field variations -- which, yes, is a phenomenon -- but
we are interested in specific "signatures" inherent in those variations.  The "signatures" will map to things like
"travelling convection vortex" or "fluxtube transfer event" or "open field lines."  These signatures are things that
are extracted from the instrument data via some algorithm.

Here's the info a bit more distilled:
* Instrument produces raw data stream
* Raw data stream is processed by some algorithm to produce some derived data signature stream (e.g., sequence
of 0's and 1's for a specific event; e.g., time-frequency sequence; whatever)
* The derived data signature/stream maps to some phenomenon of interest

After a lot of back and forth in my own head, I've momentarily decided that an instrument and its "raw data stream"
should technically be considered the same entity.  The "raw data stream" can also be considered a
"derived data stream" if it helpful... That said, the only phenomenon this raw data stream maps to is the exact
entity that the instrument measures (e.g., a raw data stream of "magnetic field variations" maps to "magnetic field
variations" by the "identity algorithm").  More generally, we are looking to process the raw data stream in
some way and extract a "data signature" or "pattern" that we argue is associated with (or caused by)
some not-directly-measured phenomenon.

Here is one possibility:

<img src="/images/ago-db-erd-3.jpg" width="500">

As I've done 1000x now, I questioned this mapping... One instrument can indeed produce many data
streams (e.g., here are three: x -> x, x^2, sqrt{x}).  However, in the pic above, I conflated
two of the concepts in my head:
* that a data signature might be independently given by multiple instruments
* that a data signature is composed of data streams from one or multiple instruments

So...we have:
* an instrument's raw data stream is associated with many data stream derivations
* one-or-many data streams from one-or-many instruments compose a data signature
* a data signature is indicative of some phenomenon

And so, another way we might map instruments to data signatures to phenomena is:

<img src="/images/ago-db-erd-4.jpg" width="500">





Resolving many-to-many relationships
* AGO dictionary table
* Instrument dictionary table
* AGO_Instrument table
* Phenomenon dictionary table
* Instrument_Phenomenon table




# References and Further Reading

## Directly Relevant to My Current Needs
* [How to Create a Database Model for an Online Store](http://www.vertabelo.com/blog/technical-articles/database-model-for-an-online-store)
* Modeling a Database for Recording Sales 
  - [Part One](http://www.vertabelo.com/blog/technical-articles/modeling-a-database-for-recording-sales-part-1)
  - [Part Two: Creating Tables for Products and Services](http://www.vertabelo.com/blog/technical-articles/modeling-a-database-for-recording-sales-part-2-creating-tables-for-products-and-services)
* [A Subscription Business Data Model](http://www.vertabelo.com/blog/technical-articles/creating-a-dwh-part-one-a-subscription-business-data-model)

## Not-so-Directly Relevant (But Still Insightful!)
* [A Children’s Party Data Model](http://www.vertabelo.com/blog/technical-articles/a-childrens-party-data-model)
* [A Database Model for a Renting Service](http://www.vertabelo.com/blog/technical-articles/rent-what-you-need-a-database-model-for-a-renting-service)
* [Constructing a Data Model for a Parking Lot Management System](http://www.vertabelo.com/blog/technical-articles/constructing-a-data-model-for-a-parking-lot-management-system)
* [Summer Is Here: A Travel Agency Data Model](http://www.vertabelo.com/blog/technical-articles/summer-is-here-a-travel-agency-data-model)
* [A Marketing Agency Data Model](http://www.vertabelo.com/blog/technical-articles/a-marketing-agency-data-model)

## Automated Geophysical Observatories
* http://www.polar.umd.edu/ago.html
* http://www.polar.umd.edu/instruments.html
* http://nova.stanford.edu/~vlf/Antarctica/AGO/ago.html
* https://web.njit.edu/~gerrard/Projects.html
