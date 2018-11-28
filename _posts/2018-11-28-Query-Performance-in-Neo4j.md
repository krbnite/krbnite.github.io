---
title: Query Performance in Neo4j
layout: post
---

I was going through the [Cypher Manaul](https://neo4j.com/docs/cypher-manual/current/query-tuning) today, and
started playing around with `EXPLAIN` and `PROFILE` to learn more about how Neo4j formulates execution
plans.  The most important piece of advice to extract is: Do not be lazy while writing your queries!  

This means, remove as many ambiguities and guess work from your query as possible:
* If you know the node's label, include it
  - Wrong: `MATCH (p {name: "Romeo"}) RETRUN p`
  - Right: `MATCH (p:Person {name: "Romeo"}) RETRUN p`
* If you know the relationship type, include it
  - Wrong: `MATCH (p {name: "Romeo"}) --> (m) RETRUN m`
  - Right: `MATCH (p:Person {name: "Romeo"}) -[:LIKES]-> (m:Movie) RETRUN m`
* If it's an important, highly-used query -- put an index on it
  - e.g., `CREATE INDEX on :Person(name)`

Playing around with Neo4j Browser on a small data set just for fun? Then sure -- it might feel good have a dirty rum 
martini, get behind the Cypher wheel, and query a bit sloppy (what's a few measly milliseconds anyway?!).  But best ye be 
puttin' that drink down, lest ye beget bad habits: the same sloppiness on large data sets in production can make your 
product feel sluggish and painful.  

You might be asking right about now: "Wtf is a dirty rum martini?" 

Never you mind!

## An Example
Honestly, the [Cypher Manaul](https://neo4j.com/docs/cypher-manual/current/query-tuning) has
a great example -- and for my own ease-of-review, much of it has been liberated from there and put 
here, though with various adjustments and additional experiments.  

**Side Note**: I found that my query profiles differed from that in the Cypher Manual.  My hypothesis is
that this is likely due to using a different version of Neo4j or Cypher (e.g., Community vs Enterprise, or just
more up-to-date version in general).

### Get the Data
```
// Movies
LOAD CSV WITH HEADERS FROM 'https://neo4j.com/docs/cypher-manual/3.5/csv/query-tuning/movies.csv' AS line
MERGE (m:Movie { title: line.title })
ON CREATE SET m.released = toInteger(line.released), m.tagline = line.tagline

// Actors
LOAD CSV WITH HEADERS FROM 'https://neo4j.com/docs/cypher-manual/3.5/csv/query-tuning/actors.csv' AS line
MATCH (m:Movie { title: line.title })
MERGE (p:Person { name: line.name })
ON CREATE SET p.born = toInteger(line.born)
MERGE (p)-[:ACTED_IN { roles:split(line.roles, ';')}]->(m)

// Directors
LOAD CSV WITH HEADERS FROM 'https://neo4j.com/docs/cypher-manual/3.5/csv/query-tuning/directors.csv' AS line
MATCH (m:Movie { title: line.title })
MERGE (p:Person { name: line.name })
ON CREATE SET p.born = toInteger(line.born)
MERGE (p)-[:DIRECTED]->(m)
```

### Single-Node Query
```
// Profile lazy node query
PROFILE
MATCH (p {name: "Keanu Reeves"}) 
RETURN p
```

Using CYPHER 3.4, the COST planner, and COMPILED runtime, this resulted in 347 total db hits in 26 ms:
  * **AllNodesScan**: 174 db hits, 9 pagecache hits, 0 pagecache misses, 173 estimated rows, 173 rows
  * **Filter**: 173 db hits, 1356 pagecache hits, 0 pagecache misses, 17 estimated rows, 1 row
  * **Produce Results**: 0 db hits, 6 pagecache hits, 17 estimated rows, 1 row
  * **Result**

```
// Profile non-lazy node query
PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) 
RETURN p
```

Using CYPHER 3.4, the COST planner, and COMPILED runtime, this resulted in 267 total db hits in 21 ms:
  * **AllNodesScan**: 134 db hits, 9 pagecache hits, 0 pagecache misses, 133 estimated rows, 133 rows
  * **Filter**: 133 db hits, 1065 pagecache hits, 0 pagecache misses, 13 estimated rows, 1 row
  * **Produce Results**: 0 db hits, 8 pagecache hits, 13 estimated rows, 1 row
  * **Result**
  
Shaved a whole 5 ms off!  This might seem like a little bit, but our toy data set only has 173 nodes and 
254 relationships.  

Can we do better?  Yes: add an index.

```
// Create an indexes and profile that again
CREATE INDEX ON :Person(name);

PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) 
RETURN p
```

Using CYPHER 3.4, the COST planner, and COMPILED runtime, this resulted in 2 total db hits in 47 ms:
  * **NodeIndexSeek**: 2 db hits, 5 pagecache hits, 1 pagecache miss, 1 estimated row, 1 row
  * **Produce Results**: 0 db hits, 5 pagecache hits, 1 pagecache miss, 1 estimated row, 1 row
  * **Result**

The time almost doubled, though the number of db hits shrank by more 133x.  The larger amount of time
is likely due to having such a small data set: indexing doesn't necessarily offer a time advantage at this
scale.  However, one can imagine that a much larger data set would likely see dramatic improvements
in time as well (will test at end of all this by increasing data set size).

```
// Drop the index before next example
DROP INDEX ON :Person(name)
```

### Multi-Node Query w/ Relationship
```
// Profile lazy node-relationship-node query
PROFILE
MATCH (p {name: "Tom Hanks"}) --> (m) 
WHERE m.released > 1994
RETURN m
```

Using CYPHER 3.4, the COST planner, and SLOTTED runtime, this resulted in 374 total db hits in 3 ms:
  * **AllNodesScan**: 174 db hits, 10 pagecache hits, 0 pagecache misses, 173 estimated rows, 173 rows
  * **Filter**: 173 db hits, 9 pagecache hits, 0 pagecache misses, 17 estimated rows, 1 row
  * **Expand(All)**: 14 db hits, 9 pagecache hits, 0 pagecache misses, 25 estimated rows, 13 rows
  * **Filter**: 13 db hits, 9 pagecache hits, 0 pagecache misses, 1 estimated row, 10 rows
  * **Produce Results**: 0 db hits, 9 pagecache hits, 0 pagecache misses, 1 estimated row, 10 rows
  * **Result**

```
// Profile semi-non-lazy node-relationship-node query
PROFILE
MATCH (p:Person {name: "Tom Hanks"}) -[:ACTED_IN]-> (m) 
WHERE m.released > 1994
RETURN m
```

Using CYPHER 3.4, the COST planner, and SLOTTED runtime, this resulted in 292 total db hits in 20 ms:
  * **AllNodesScan**: 134 db hits, 8 pagecache hits, 0 pagecache misses, 133 estimated rows, 133 rows
  * **Filter**: 133 db hits, 7 pagecache hits, 0 pagecache misses, 13 estimated rows, 1 row
  * **Expand(All)**: 13 db hits, 7 pagecache hits, 0 pagecache misses, 17 estimated rows, 12 rows
  * **Filter**: 12 db hits, 7 pagecache hits, 0 pagecache misses, 0 estimated row, 9 rows
  * **Produce Results**: 0 db hits, 7 pagecache hits, 0 pagecache misses, 0 estimated row, 9 rows
  * **Result**
  
```
// Profile non-lazy node-relationship-node query
//   -- that is, specify both relationship and second node label
PROFILE
MATCH (p:Person {name: "Tom Hanks"}) -[:ACTED_IN]-> (m:Movie) 
WHERE m.released > 1994
RETURN m
```

Using CYPHER 3.4, the COST planner, and SLOTTED runtime, this resulted in 481 total db hits in 37 ms:
  * **AllNodesScan**: 41 db hits, 23 pagecache hits, 0 pagecache misses, 40 estimated rows, 40 rows
  * **Filter**: 40 db hits, 22 pagecache hits, 0 pagecache misses, 1 estimated rows, 31 row
  * **Expand(All)**: 154 db hits, 22 pagecache hits, 0 pagecache misses, 5 estimated rows, 123 rows
  * **Filter**: 246 db hits, 22 pagecache hits, 0 pagecache misses, 0 estimated row, 9 rows
  * **Produce Results**: 0 db hits, 22 pagecache hits, 0 pagecache misses, 0 estimated row, 9 rows
  * **Result**

Actually seems to have gotten worse... Mostly at the **Expand(All)** and subsquent **Filter** steps...but why? And 
would this trend continue onto a larger data set where performance really starts to matter?

Ok... Well, moving onto indexes...

```
// Create an index on :Person(name) and profile that again
CREATE INDEX ON :Person(name);

PROFILE
MATCH (p:Person {name: "Tom Hanks"}) -[:ACTED_IN]-> (m:Movie) 
WHERE m.released > 1994
RETURN m
```

Using CYPHER 3.4, the COST planner, and SLOTTED runtime, this resulted in 39 total db hits in 1 ms:
  * **NodeIndexSeek**: 2 db hits, 7 pagecache hits, 0 pagecache misses, 1 estimated rows, 1 rows
  * **Expand(All)**: 13 db hits, 7 pagecache hits, 0 pagecache misses, 1 estimated rows, 12 rows
  * **Filter**: 24 db hits, 7 pagecache hits, 0 pagecache misses, 0 estimated row, 9 rows
  * **Produce Results**: 0 db hits, 7 pagecache hits, 0 pagecache misses, 0 estimated row, 9 rows
  * **Result**


```
// Drop the index on :Pereson(name) and create an index on :Movie(released) 
DROP INDEX ON :Person(name);

CREATE INDEX ON :Movie(released);

PROFILE
MATCH (p:Person {name: "Tom Hanks"}) -[:ACTED_IN]-> (m:Movie) 
WHERE m.released > 1994
RETURN m
```

Using CYPHER 3.4, the COST planner, and SLOTTED runtime, this resulted in 433 total db hits in 45 ms:
  * **NodeIndexSeekByRange**: 33 db hits, 18 pagecache hits, 1 pagecache misses, 1 estimated rows, 31 rows
  * **Expand(All)**: 154 db hits, 18 pagecache hits, 1 pagecache misses, 5 estimated rows, 123 rows
  * **Filter**: 246 db hits, 18 pagecache hits, 1 pagecache misses, 0 estimated row, 9 rows
  * **Produce Results**: 0 db hits, 18 pagecache hits, 10 pagecache misses, 0 estimated row, 9 rows
  * **Result**

```
// Recreate the index on :Pereson(name) so that we now have two 
// indexes (:Person(name) and :Movie(released)) 

CREATE INDEX ON :Person(name);

PROFILE
MATCH (p:Person {name: "Tom Hanks"}) -[:ACTED_IN]-> (m:Movie) 
WHERE m.released > 1994
RETURN m
```

Using CYPHER 3.4, the COST planner, and SLOTTED runtime, this resulted in 379 total db hits in 29 ms:
  * Two starting point that converge in the next step:
    - **NodeIndexSeek**: 3 db hits, 10 pagecache hits, 1 pagecache misses, 1 estimated rows, 1 row
    - **NodeIndexSeekByRange**: 33 db hits, 10 pagecache hits, 0 pagecache misses, 1 estimated rows, 31 rows
  * **CartesianProduct**: 0 db hits, 10 pagecache hits, 1 pagecache misses, 1 estimated rows, 31 rows
  * **Expand(Into)**: 343 db hits, 10 pagecache hits, 1 pagecache misses, 0 estimated rows, 9 rows
  * **Produce Results**: 0 db hits, 10 pagecache hits, 1 pagecache misses, 0 estimated row, 9 rows
  * **Result**

...and that second index on :Movie(released) reduced the performance of just having a single
index on :Person(name).  Again, not sure why this is other than there is a certain cost to 
using an index.  That cost might not grow as fast as your data set, so for a larger data set
maybe 2 indexes would be better!  However, that is certainly not true on this small data set.

Moral?  Not sure.  
* Indexes are great -- sometimes
  - Other times, they can increase the db hits and/or time
  - Too many indexes can affect performance
* Specifying labels and relationship types is important -- mostly 
  - We did see some performance reduction on a small data set
* Not sure how these lessons expand to much larger data sets...


## Larger Toy Data Set
So at one point above, I promised to re-do everything on a larger data set... Well, consider
that promise broken -- for now!  This post has gotten too long, and it seems a follow-up post
would be better.

So, until next time -- ta, ta!
