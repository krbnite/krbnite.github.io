---
layout: post
title:  Semi-Colon-Separated Queries in Neo4j Desktop
tags: neo4j easi
---

When I first began working at WWE, my only SQL experience was through online courses, tinkering with SQLite,
and using sample datasets to solve toy problems.  My world was about to change.

The WWE Network is like "Netflix for Wrestling," boasting anywhere between 1-2 million daily active subscribers and hordes 
of wrestling-related content. In an effort to better understand our customers and optimize our content, we were 
interested in any kind of customer data that you can imagine Netflix would be interested in (e.g., who's viewing what,
for how long, at what time, and so on).  I was set up with a Redshift account and asked to do some seemingly 
simple things...  Until I saw how many tables we had from every which way and direction.  Whoa.  I plunged right into
Join Central.  

As a "big data" newb, a way to keep things straight in my head was to take advantage of creating
sequences of temporary tables.  Often, I was connected to Redshift from R or Python, and these 
sequences were all wrapped up in a single string, queries separated by semi-colons, and submitted to Redshift 
all at once. This was also important when submitting a string of CREATE TABLE commands -- and probably for
other reasons too!  

Point is:  This is a story about liking semi-colon separation of queries!  That, and the fact that, by default, I couldn't
do it for multiple Cypher queries in Neo4j Desktop / Browser. Sometimes you can
do multiple things anyway, like have a sequence of MATCH, MERGE, and CREATE statements.  But for more complex queries,
results can get unpredictable... And sometimes you just want to separate statements with a damn semi-colon!  

So, there's gotta be a way ... right?

::Drum Roll::

Yes:  **You can do this my clicking on the Settings icon, then checking the box that says "Enable multi statement
query editor".**

\</endUpdate>
