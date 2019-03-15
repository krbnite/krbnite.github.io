---
title: Data Structures in Neo4j
layout: post
tags: databases neo4j easi
---

What's cool about Neo4j is that you can output the data almost any way you want: you can return 
nodes (which are basically shallow JSON documents), or anything from flat tables to full-on JSON document
structures.  

For example, take the movie data set.  Using projection and aggregation, you can return "actor documents",
which have an actor at the top level of heirarchy, and a movies key, which stores all movie info and what
roles they played.  

```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
RETURN p { .*,  movies: COLLECT(m { .*, roles: r.roles})}
```

Better, since this is a graph database, you can effortlessly invert the relationship and 
return "movie documents" that list all actors and their roles in that movie.  

```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
RETURN m { .*,  actors: COLLECT(p { .*, roles: r.roles})}
```

The query is almost no different, except in how you aggregate and format the output.

Very cool!

