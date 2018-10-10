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
can use ERDs to construct fully functional, normalized databases.

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

Candidate entity list
* instruments
* AGOs
* times
* phenomena
* raw data stream
* derived data streams / data products

//// PICTURE OF RELATIONSHIPS /////

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
