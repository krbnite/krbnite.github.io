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
// Profile lazy node query
PROFILE
MATCH (p {name: "Keanu Reeves"}) 
RETRUN p

// Profile non-lazy node query
PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) 
RETRUN p

// Create an indexes and profile that again
CREATE INDEX ON :Person(name)
PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) 
RETRUN p

// Drop the index before next example
DROP INDEX ON :Person(name)
```

### Multi-Node Query w/ Relationship
```
// Profile lazy node-relationship-node query
PROFILE
MATCH (p {name: "Keanu Reeves"}) --> (m) 
WHERE m.released > 1994
RETRUN m

// Profile non-lazy node-relationship-node query
PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) -[:LIKES]-> (m:Movie) 
WHERE m.released > 1994
RETRUN m

// Create an index on :Person(name) and profile that again
CREATE INDEX ON :Person(name)
PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) -[:LIKES]-> (m:Movie) 
WHERE m.released > 1994
RETRUN m

// Create an index on :Movie(released) and profile it one more time!
CREATE INDEX ON :Movie(released)
PROFILE
MATCH (p:Person {name: "Keanu Reeves"}) -[:LIKES]-> (m:Movie) 
WHERE m.released > 1994
RETRUN m
```



