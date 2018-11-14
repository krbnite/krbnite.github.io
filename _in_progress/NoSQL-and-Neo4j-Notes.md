
Relational databases might better be called "tabular databases" in colloquial parlance.  The "relational" in
"relational database" refers to "relational algebra," which is an algebra on table-like objects.  A graph
database like Neo4j is truly a "relational" database if one interprets "relational" as "focusing on relationships."  In
Neo4j, relationships between data elements are promoted to first class citizens in the database, whereas in a
relational database, relationships are hidden behind and muddled up by joins and join tables (and in a key-value or
document database, relationships are defined outside the database, e.g., at the application layer).  Where a relational
database really gets clunky, in my opinion, is when there are heirarchical, recursive, and/or more complex 
graph relationships in the data between the same entity.  Social networks are best at making this obvious, which is why 
they are used so often as motivators for graph databases.  ...  



There are several kinds of graph databases, which have differing data models.  Neo4j's data model is called
the "property graph" model, where you have nodes (entities), node types (labels), node attributes, 
edges (relationships), edge types (labels), and edge attributes.

----------------------

Some databases are optimized for online transactional processing (OLTP), e.g., many traditional relational
databases, which are great at small, random updates and insertions.  Other databases are optimized for online
analytical processing (OLAP), e.g., Redshift, which is great at aggregating large amounts of data very 
quickly.  Many traditional relational databases are optimized for consistency and availability, but are poor at 
being distributed (horizontally scaled) over many nodes in varying geographic regions.  Key-value and document 
databases loosen up on consistency and/or availability in favor of partitition tolerance to allow for strong 
horizontal scaling capabilities. Key-value stores are great for quick read-and-write access on blobs of data
that do not need to be joined together for analysis, and which do not need to be queried by value.  Document
stores step it up a bit and let you query by value, but are still seem suboptimal for exploring relationships between
the document data. On the front end, most relational databases use SQL as their query language, which is well known
and a safe bet for finding talent.  However, many NoSQL databases use or can use SQL or a SQL-esque query
language too.  Point is: when choosing a database, you should assess what your needs are and will be, develop an
idea of where your pain points might arise, and seek to understand what tradeoffs can be made -- and their downstream
consequences.  

Importantly, for some applications and projects, it might not really matter what you choose. For example, if you
do not expect many concurrent users, or very large data sets, or heavy analytical workloads, then you can basically
choose anything and make it work -- but the safest bet in this situation then is to just choose a tried-and-true
relational database, like Postgres.  That said, if the project isn't mission critical and there are no foreseeable 
pain points (like those listed above), then it might also be a good time to explore a nontraditional option, 
like a document store or graph database.  

-------------------------

Something I didn't know (from Neo4j's [tutorial](https://neo4j.com/graphacademy/online-training/getting-started-graph-databases-using-neo4j/part-1/)):
> "A graph database is an online database management system with Create, Read, Update and Delete (CRUD) operations 
> working on a graph data model. Graph databases are generally built for use with On line transaction processing (OLTP) 
> systems. Accordingly, they are normally optimized for transactional performance, and engineered with transactional 
> integrity and operational availability in mind."

For what I'm working on, we don't necessarily need transactions... If anything, users will be querying the same
data set.  But then again, the data set itself or the queries don't necessarily qualify as "heavy analytical workloads." Also,
not sure too many users will be querying at the same time.  So maybe this doesn't matter.


------------------------------------------------------

A traversal is **how** you query and navigate through a graph, following some logic or set of rules.  Using Gremlin
in Neo4j, you can be fairly specific.  Using Cypher, you can be declarative -- "Show me all X that has Y."

A path is how you get from Node1 to Node2. 
* This path has length 1:  N1 - N2
* This path has length 0: N1
* This path has length 1: N1 - N1 (self-related)

Neo4j Schema:  Though Neo4j is schema optional, you can gain performance and modeling benefits by 
specifying graph schema.  

Neo4j also supports indexing (use for repetitive queries to gain speed) and constraints (use to control your 
data).

## Download Neo4j Desktop
* Go here: https://neo4j.com/download/
* Fill out form and DL disk image (on Mac)
* You can play for 30 days, but they want you to register... Can do via Gmail, or Github, etc
  - I used my Github acct
* If first time, choose to create a new database ("New Graph") -> "Create a Local Graph" (other option is to connect to a remote graph)
  - make a password for this new graph (e.g., th1sIsN0tAP2ssW0rd)
* Click on "start"
* After it starts, open the Neo4j Browser


Lots of good info here:
https://neo4j.com/download-thanks-desktop/?edition=desktop&flavour=osx&release=1.1.10&offline=true

------------------------------------------------

From the "learning" section linked to in Neo4j browser
* Nodes are the name for data records in a graph
* Data is stored as Properties
* Properties are simple name/value pairs
* Nodes can have zero or more labels
  - Labels allow you to group nodes together (similar to how a table groups together rows)
* Labels do not have any properties (similar to how a table has a name)
  - strings, numbers, booleans
* Nodes may have relationships between each other
  - a relationship always has a direction and a type
  - I think Neo4j does allow for "bidirectional" relationships on the surface (under the hood, probably just two directed relationships)
* Relationship are also data records in Neo4j
* Relationships may have properties

> " In Neo4j, the keys are strings and the values are the Java string and primitive data types, plus arrays of these types."

> "Nodes can be tagged with one or more labels. Labels group nodes together, and indicate the roles they play within the dataset."

Can't emphasize how cool this property is: a single node can be listed :Animal, :Mammal, :Cat, :Tabby; and
one can query for any set of lables w/ no friction.  

A graph DB can be thought of like a relational DB in some regards.  For example, imagine creating :Person nodes,
each of which has properties (name, gender, age).  Without any relationships defined, this is really similar
to just have a Person table with name, gender, and age columns.  However, the graph version allows you to 
have extra columns when necessary, e.g., one person might have eye_color listed.  Other :Person nodes do not
need to track the lack of data in this column; it is implied by not having the property (there are no nulls to take up space).  Furthermore, once
one begins defining relationships, it is like having a join table, but without an actual table.  All data
is graph-connected and graph-stored on disk... That is, no foreign keys necessary; no need to scan a whole table
looking for a relationship.  Two nodes are directly linked conceptually and on disk...

RDBMS -> Graph:
* a row is node
* a label is a table name

## Cypher
`:play cypher` in Neo4j browser to learn basics









===============================================

convert(thisisatest)

-----------------------------------------

From Oreilly Graph Database book:

Performance is roughly invariant to the size of the data set:
> "One compelling reason, then, for choosing a graph database is the sheer performance
> increase when dealing with connected data versus relational databases and NOSQL
> stores. In contrast to relational databases, where join-intensive query performance
> deteriorates as the dataset gets bigger, with a graph database performance tends to
> remain relatively constant, even as the dataset grows. This is because queries are
> localized to a portion of the graph. As a result, the execution time for each query is
> proportional only to the size of the part of the graph traversed to satisfy that query,
> rather than the size of the overall graph."

Flexibility.  Sometimes you want a table to perform two duties, but you would have to replicate the
table, create a view, or whatever...  In Neo4j, you can just use a second label.  For example, a :Person
node might also be an :Actor node.  Or in my case, a :Phenomenon node might also be a :Motif node.
> "Graphs are naturally additive, meaning we can add new kinds of relationships, new
> nodes, new labels, and new subgraphs to an existing structure without disturbing
> existing queries and application functionality."


I tend to like how the Neo4j folk frequently say, "Relational databases lack relationships." :-p

> "Relationships do exist in the vernacular of relational databases, but only at modeling
> time, as a means of joining tables. ...we often need to disambiguate the semantics of the relation‐
> ships that connect entities, as well as qualify their weight or strength. Relational rela‐
> tions do nothing of the sort. Worse still, as outlier data multiplies, and the overall
> structure of the dataset becomes more complex and less uniform, the relational
> model becomes burdened with large join tables, sparsely populated rows, and lots of
> null-checking logic. The rise in connectedness translates in the relational world into
> increased joins, which impede performance and make it difficult for us to evolve an
> existing database in response to changing business needs."

One might think a document store can be used to make graph-like structures, but be warned: you
would be responsible for all constraints, relationship integrity and management, etc.  That is,
you can have customer document and order documents, for example.  Assuming you have assigned unique IDs
to each order, you can "link" a customer document to and order document by including an ORDERS array
in the customer document.  If a customer document gets deleted, if you do not make some code at the
application layer to clean up the associated order documents, you will have "hanging documents" that 
just add to the overhead in the database.  This is just a best-case example.  Consider product documents:
you might have unique product IDs, then have a product array in each order document.  So now you might
be able to quickly query something like "Show me all products purchased by customer X": start at 
customer X, look up each order in the order array, look up each product in the orders' product arrays,
and de-dup the list.  However, can you find me all customers that bought product Y?  Yes, but now
you are dealing with full set scans of orders: look through each order, compile list of associated
customers with product ID in order, and de-dup the customer list.  It's just as simple to write,
but the first operation is proportional only to the number of orders made by customer X, while the
second operatio is proportional to all orders in the datastore.  In a graph database, both operations
would be computationally simple...


### Equivalent Cypher queries
```
MATCH (a:Person {name:'Jim'})-[:KNOWS]->(b)-[:KNOWS]->(c),
 (a)-[:KNOWS]->(c)
RETURN b, c
```

```
MATCH (a:Person)-[:KNOWS]->(b)-[:KNOWS]->(c), (a)-[:KNOWS]->(c)
WHERE a.name = 'Jim'
RETURN b, c
```


## Data Modeling
* Conceptual model
* Logical model
* Physical model

In both graph and relational modeling, the conceptual model is a graph that shows the entities and
relationships in the system being modeled.  In relational modeling, one then captures these details
more rigorously with entity relationship diagrams (ERDs).  This is often where many-to-many relationships
are identified and normalized by creating join tables and foreign keys.  In graph modeling, the logical model is often
nearly the same as the conceptual model -- no join tables or foreign keys necessary.  And to be clear,
the absence of join tables and foreign keys does not imply the absense of what those things are used for:
mapping relationships between entities. No, in fact the relationship mapping is stronger, more direct, and obvious,
to the human and to the computer.  Crazily enough, after all the work of normalizing the database in relational
modeling, the database engineer often must then consider performance and query patterns, and thoughtfully
de-normalize the data model (this is b/c fully normalized data models can often be a huge computational burder
and no suitable for quick, online system needs).  


> "In the early stages of analysis, the work required of us is similar to the relational
> approach: using lo-fi methods, such as whiteboard sketches, we describe and agree
> upon the domain. After that, however, the methodologies diverge. Instead of trans‐
> forming a domain model’s graph-like representation into tables, we enrich it, with the
> aim of producing an accurate representation of the parts of the domain relevant to
> our application goals. That is, for each entity in our domain, we ensure that we’ve
> captured its relevant roles as labels, its attributes as properties, and its connections to
> neighboring entities as relationships. ... No tables, no normalization, no denormaliza‐
> tion. Once we have an accurate representation of our domain model, moving it into
> the database is trivial."

### Data Model Checks
* "...check that the graph reads well. We pick a start node, and then follow relationships
to other nodes, reading each node’s labels and each relationship’s name as we go.
Doing so should create sensible sentences. "
* "... adopt a design-for-queryability mindset. To validate that the graph
supports the kinds of queries we expect to run on it, we must describe those queries."

Craft specific questions, and write out their Cypher statements.


### Variable Length Queries
One thing we need to do:
* Which devices map to this phenomenon?
  - Since this can be a variable-length path of transformations, we need a query that can support that
  
This query allows for a path length up to 5 relationshps without regard to the type of intervening
nodes or relationships... Depending how connected our graph is, this may or may
not be a great query, but it shows that variable path length queries are possible.
```
MATCH (p:Phenomenon)-[*1..5]-(device:Device)
WHERE p.name = 'PTSD'
RETURN DISTINCT device
```

### Ways to search
* use labels to produce meaningful sets
* use variable length searches when necessary
* constrain searches by specifying path elements, e.g., incoming vs outgoing relationships

## Cross-Domain Modeling
Neo4j book uses different line types for relationships from different domains, e.g., dotted vs solid lines.


"...[use] relationship names and node labels ... to structure the graph and estab‐
lish the semantic context for each node."

"Relationships help both partition a graph into separate domains and connect those
domains. As we can see from our Shakespeare example, the property graph model
makes it easy to unite different domains—each with its own particular entities, labels,
properties, and relationships—in a way that not only makes each domain accessible,
but also generates insight from the connections between domains."


Important to note that the property graph model not only promotes relationships to
first-class citizenry, but also "table names" (labels):
> "Labels are first class citizens of the property graph model. Besides indicating the roles
> different nodes play in our domain, labels also allow us to associate metadata with
> nodes to which those labels are attached. We can, for example, index all nodes with a
> User label, or require that all nodes with a Customer label have a unique email property value."

## Creating the database
Example:
```
CREATE (shakespeare:Author {firstname:'William', lastname:'Shakespeare'}),
 (juliusCaesar:Play {title:'Julius Caesar'}),
 (shakespeare)-[:WROTE_PLAY {year:1599}]->(juliusCaesar),
 (theTempest:Play {title:'The Tempest'}),
 (shakespeare)-[:WROTE_PLAY {year:1610}]->(theTempest),
 (rsc:Company {name:'RSC'}),
 (production1:Production {name:'Julius Caesar'}),
 .
 .
 .
```

This code creates nodes and their properties, and then maps relationships (and their properties)
between the nodes.

**We should document all our CREATE statements in a Bitbucket repo... This way, the DB (since it is
relatively small) can be recreated from scratch, if/when necessary.**

What is crazy about Cypher and the property graph model is that, when inputting new data that
spans the database, you just have to create the corresponding nodes (from your "node dictionary") 
and connect them with the appropriate relationships (see your "relationship directory").  In other words,
you do not have deal with join tables and foreign keys, etc... 

> "We can modify the graph at a later point in time in two different ways. We can, of
> course, continue using CREATE statements to simply add to the graph. But we can also
> use MERGE, which has the semantics of ensuring that a particular subgraph structure of
> nodes and relationships—some of which may already exist, some of which may be
> missing—is in place once the command has executed. In practice, we tend to use
> CREATE when we’re adding to the graph and don’t mind duplication, and MERGE when
> duplication is not permitted by the domain."

-------------------

## INDEXES
Traversal start from somewhere -- a starting point called a "bound" or "anchored" node. You
can set up one-or-many anchored nodes at the beginning of a Cypher query.  For example, if you
know the starting and ending nodes you want, and want to explore pathways between them.

```
-- starting point(s) in graph
MATCH (ago5:AGO {name: 5}),
    (cme:Phenomenon {name: 'CME'})
```

Though the traversals implemented underneath the Cypher hood might be optimized, the initial
search for the starting node might not be: this is why you create indexes.

Indexes can be created on label(property1, ...) combinations.

```
CREATE INDEX ON :AGO(name)
```

To be clear, lookups like `MATCH (ago5:AGO {name: 5})` do not require indexes, but performance is
greatly improved with indexes.

## CONSTRAINTS
We probably want some properties to be unique, like primary keys in relational tables.  We
can get this property on nodes by creating a constraint:
```
CREATE CONSTRAINT ON (a:AGO) ASSERT a.name IS UNIQUE
```

One can "promise" to keep a property unique, or code it at the application layer, etc, but
creating a "constraint" actually enforces it at the database level: you simply will not be able
to break the rule! (This is a good thing.)



```
MATCH (ago5:AGO {name: 5}),
    (cme:Phenomenon {name: 'CME'}),
    (ago5)-[:Provides|Feeds_Into|Spits_Out*1..3]->(:DataStream)
      -[:Contains]->(sig:DataSignature)-[:Indicative_Of*1..10]->(cme)
RETURN DISTINCT sig.name as signature
```


## Aggregating, Ordering, Filtering, and Limiting
It's all there:
```
MATCH (theater:Venue {name:'Theatre Royal'}),
 (newcastle:City {name:'Newcastle'}),
 (bard:Author {lastname:'Shakespeare'}),
 (newcastle)<-[:STREET|CITY*1..2]-(theater)
 <-[:VENUE]-()-[p:PERFORMANCE_OF]->()
 -[:PRODUCTION_OF]->(play)<-[:WROTE_PLAY]-(bard)
RETURN play.title AS play, COUNT(p) AS performance_count
ORDER BY performance_count DESC
```


## With Piping

> "Sometimes it’s just not practical (or possible) to do
> everything you want in a single MATCH. The WITH clause allows us to chain together
> several matches, with the results of the previous query part being piped into the next."

Simple example:
```
MATCH (bard:Author {lastname:'Shakespeare'})-[w:WROTE_PLAY]->(play)
WITH play
ORDER BY w.year DESC
RETURN collect(play.title) AS plays
```

> "WITH helps divide and conquer complex queries by allowing us to
> break a single complex query into several simpler patterns."

LEFT OFF ON P 63 (ID'ing nodes and relationships)


-------------------------------------------------------------------------


Been working on a movie database, and came up with this nice query that shows a lot of
what you can do w/ Cypher queries:
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH {name: p.name, num_movies: count(*)} AS temp
WHERE temp.num_movies > 2
RETURN temp.name AS name, temp.num_movies AS num_movies
```

That second line is a nice aggregation step, which allows me to then filter by
an aggregated variable in the third line.  In the fourth line, we could just
return the dictionary structure (`RETURN temp`), but I flatten it into a 
tabular row/col structure.  Lots going on here!  Lots of potential.


Constraints:
```
CREATE CONSTRAINT ON (movie:Movie) ASSERT movie.title IS UNIQUE
```

Indexes:
```
CREATE INDEX ON :Actor(name)
```


### CSV
Before loading data, it is important to create indexes and constraints so that
data is imported in the way you intend.  

Read this:
* https://neo4j.com/docs/developer-manual/current/get-started/cypher/importing-csv-files-with-cypher/

-------------

Another cool query:
```
match p=(p1:Person)-[*]-(p2:Person)
with nodes(p) as nodes
return distinct nodes[0].name, nodes[-1].name
```

This one is asking for any relationships between 2 people... There can be all sorts of
ACTED_IN, PRODUCED, DIRECTED relationships connecting countless people... We just extract
the beginning and ending :Person node...  

## Simple CASE Statement
```
MATCH (p:Person)
WITH p,
  CASE p.eye_color
    WHEN 'brown' THEN 1
    WHEN 'blue' THEN 2
    WHEN 'green' THEN 3
    ELSE 4
  END AS result
RETURN p.name, result
```
Note that WITH allows you to pipe results from one clause into another, however you must
explicitly list which variables get piped.  That is, if we did not list 'p' after WITH,
we could still do the case statement, but 'p' would not be defined for the RETRUN statement.


## Generic CASE Statement
```
// This is a comment
MATCH (m:Movie)
RETURN m.title,
  CASE 
    WHEN m.released < 1983 THEN 'old'
    WHEN 1983 <= m.released < 2005 THEN 'prime'
    ELSE 'newfangled'
  END AS result
```


## What properties does this label have?
Neo4j is schema-optional... Many times it is schema-free or schema-implied.  Most nodes with 
the same label have the same basic properties, but some have a few additional properties, while
a few others might have a few less, etc.  How can you get a grip on what properties exist within
the entire set of nodes with a given label?
```
MATCH (p:Person)
UNWIND keys(p) AS fields
RETURN DISTINCT fields
```


## How to find if the same actor is duplicated across nodes
In SQL, you can just SELECT actor_name, COUNT(\*) AS cnt FROM tbl GROUP BY actor_name HAVING cnt > 1.
Easy peasy! 

So how do you do the same in Neo4j?

```
MATCH (a:Person)
WITH a.name AS name, collect(a) AS nlist, count(*) AS cnt
WHERE cnt > 1
RETURN name, cnt
```

Aggregations in Neo4j are slightly more implicit in appearance, so it's not always 
obvious what to do, especially if thinking in terms of SQL...  Basically, that 2nd line
is the GROUP BY statement: we are collecting all nodes associated with the same name.

## Setting Parameters in Neo4j Browser (environment variables)
```
:param kr => 'Keanu Reeves'
MATCH (a:Person {name: $kr}) RETURN a
```


## DB functions to do some basic tasks
```
// list all labels
CALL db.labels()

// list all indexes
CALL db.indexes()

// list all relationship types
CALL db.relationshipTypes()

```

## Check if label L nodes ever have other labels
```
// This will return "Person", "otherLabel1", ....
MATCH (p:Person)
UNWIND labels(p) AS node_labels
RETURN DISTINCT node_labels

// Alternatively, this will just return other labels
MATCH (p:Person)
UNWIND labels(p) AS node_labels
WITH node_labels 
  WHERE node_labels<>"Person"
RETURN DISTINCT node_labels
```

For example, :Person nodes might sometimes be labeled with :Actor, :Director, etc.

## Find all property keys associated w/ Label L
```
MATCH (p:Person)
UNWIND keys(p) as person_keys
RETURN DISTINCT person_keys
```

## Find all relationship types associated w/ label L

```
// Any relationships to any node
MATCH (p:Person)-[r]-()
RETURN DISTINCT type(r)

// Only outgoing relationships to any node
MATCH (p:Person)-[r]->()
RETURN DISTINCT type(r)

// Only incoming relationships to any node
MATCH (p:Person)<-[r]-()
RETURN DISTINCT type(r)

// Only incoming relationships to from a specific node type
MATCH (p:Person)<-[r]-(:Movie)
RETURN DISTINCT type(r)
```


## Find all relationship types like (a)-[r1]->(b)-[r2]->(a), where r1==r2
This can be stuff like "IS_TWIN_OF" or "IS_FRIENDS_WITH".  
```
MATCH (a)-[r1]->(b)-[r2]->(a)
WHERE type(r1)=type(r2)
RETURN DISTINCT type(r1)
```

## Find any person who is related to a movie in more than one way
```
// You might think this will work...
MATCH (a:Person)-[r1]->(b:Movie), (a)-[r2]->(b)
WHERE type(r1)<>type(r2)
RETURN a.name, type(r1), type(r2), b.title

// But that actually has many duplicates (1 row for (r1,r2) and a 2nd row for (r2,r1))

// Here is a better way
MATCH (a:Person)-[r1]->(b:Movie), (a)-[r2]->(b)
WHERE type(r1)<>type(r2)
WITH a.name+': '+b.title AS group, a.name AS name, 
    b.title as title, collect(distinct type(r1)) as rlns
RETURN name, rlns, title

// The above will return things like:  
//     Kevin Urban, [ACTED_IN, DIRECTED], Nothing
//     Schmevin Schmurban, [ACTED_IN, WROTE, PRODUCED], Something
// If you only wanted to return people w/ exactly 2 roles, you can add in
//   a WHERE clause
MATCH (a:Person)-[r1]->(b:Movie), (a)-[r2]->(b)
  WHERE type(r1)<>type(r2)
WITH a.name+': '+b.title AS group, 
    a.name AS name, 
    b.title as title, 
    collect(distinct type(r1)) as rlns, 
    count(distinct type(r1)) as cnt
  WHERE cnt =2
RETURN name, rlns, title

// Simpler way: use size() fcn in WHERE statement
MATCH (a:Person)-[r1]->(b:Movie), (a)-[r2]->(b)
  WHERE type(r1)<>type(r2)
WITH a.name+': '+b.title AS group, 
    a.name AS name, 
    b.title as title, 
    collect(distinct type(r1)) as rlns 
  WHERE size(rlns) = 2
RETURN name, rlns, title
```

## List Comprehensions
These are very similar to list comprehensions in Python except that the syntax is 
slightly reversed... An example will show what I mean:

Python
```python
[x**3 for x in range(0,10) if x % 2 == 0]
```

Cypher
```cypher
[x IN range(0,10) WHERE x % 2 = 0 | x^3]
```


## Pattern Comprehensions

```
// Example
MATCH (a:Person { name: 'Tom Cruise' })
RETURN [(a)-->(b) WHERE b:Movie | b.released] AS years

// You could also do this particular query w/o Pattern Comprehension
MATCH (p:Person {name:"Tom Cruise"})-->(m:Movie)
RETURN collect(DISTINCT m.released) AS years
```

The example is simple, so the pattern comprehension might seem unmotivated... However,
it's another tool in the toolbelt -- just like comprehensions in Python.  

## Map Projects
Say you want output associated with each actor to be contained wholly in one 
object.  That is, your output is a "single column of objects", rather than having
multiple columns with the same info...

```
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
RETURN distinct actor
```

This returns a column of objects (actor maps).  

But what if you want to return some calculated values, say the number of movies that
actor was in?

You can try a map projection:
```
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
WITH actor, count(movie) AS n_movies, collect(movie.title) as movies
RETURN actor { .*, n_movies}
```

Sure, that map can be easily flattened into multi-column rows:
```
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
WITH actor, count(movie) AS n_movies, collect(movie.title) as movies
RETURN actor.name, actor.born, n_movies
```

And so you might be wondering: What's the point?  The point is, you have infinte power
of the gods to emit output in any shape your wee heart desires!

For example, this technique proves valuable when a one-to-one flattening is not the case.  You can 
return a JSON-like object in general instead of being forced
to tabularize the output.  This is good for "impedance mismatch" issues.

Here is another example:
```
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
WITH actor, count(movie) AS n_movies, collect(movie.title) as movies
RETURN actor { .*, n_movies, movies}
```

Moral of the story: In Neo4j, you can mold and shape the "row, col" structure
of the output:  It can be like the tables of old with each column supporting atomic values,
or like a list of JSON objects, or anything in between (e.g., mostly relational looking output
with a JSON column).

## Shortest Path
Say we are looking at AGO5: what instrument should we choose to track CMEs?  One way to 
do this is to figure out what is the shortest path from the AGO5 :AGO node to the 
CME :Event node, and to note which instrument lies in that path. (This would likely
entail the instrument with the least amount of data transformations required to map
out an event stream.)

```
MATCH (ago:AGO { name: 'AGO5' }), 
  (event:Event { name: 'CME' }), 
  p = shortestPath((ago)-[*..15]-(CME))
RETURN p
```

There is also an `allShortestPaths()` function...which presumably returns all
paths of shortest length L.  (If so, then how does `shortestPath()` choose?)

---------------------------------------

## Optional Match
Optional Match is weird... The Neo4j docs likens it to a SQL outer join.  In a SQL outer join,
if there is not match between tables, nulls are placed -- e.g., imagine joining a table with customer
information with a table with info about random people: where your customer matches into the 
"random person" table, you will acquire more info, and were it doesn't, the extra fields will be filled
with nulls......   Anyway, an optional match is like that: if a pattern doesn't exist, it will
be filled with nulls....

-------------------


## Return only nodes with (or without) a certain property

```
// Return only those :Person nodes w/ birth year filled in
MATCH (p:Person)
  WHERE exists(m.born)
RETURN collect(m.name)

// Return :Person nodes where this property hasn't been filled in
MATCH (p:Person)
  WHERE NOT exists(m.born)
RETURN collect(m.name)
```


## Some string stuff
```
MATCH (p:Person)
WHERE p.name STARTS WITH 'K' AND p.name CONTAINS 'ee'
RETURN collect(p.name)

MATCH (p:Person)
WHERE p.name ENDS WITH 'S'
RETURN collect(p.name)

MATCH (p:Person)
WHERE p.name =~ '.+eanu.+'
RETURN collect(p.name)
```

Regular expressions follow Java regular expression rules.

----------------------

Find all devices without an accelerometer.
```
MATCH (dev:Device)
WHERE dev.accelerometer IS NULL
```

Find all instruments with a PPG, but no GSR:
```
MATCH (dev:Instrument)
WHERE dev.ppg IS NOT NULL AND dev.gsr IS NULL
```

Give random ordering:
```
with [1,2,3,4,5] as ls
unwind ls as uls
return uls, toInteger(100*rand()) as r
order by r
```



Combine two lists item by item into list of doublets:
```
with ['a','b','c','d'] as x
with x, range(0,size(x)-1) as s
with [n in s | [n, x[n]]] as res
return res
```


-----------------------------



#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!

# Printing Paths Prettily
So this is my first attempt...  Not very pretty, but it at least prints the label, relationship, label, 
relationship... Specifically, this looks for the shortest path between two specified nodes (up to 20 hops
longest).  
```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[*..20]-(t:Person {name:"Tom Hanks"}))
WITH extract(node IN nodes(path) | coalesce(labels(node),'')) AS node_labels,
     extract(rel IN relationships(path) | type(rel)) AS rel_types
WITH node_labels, rel_types, 
  range(0,size(node_labels)-1) AS szn, 
  range(0,size(rel_types)-1) AS szr
WITH [i IN szn | [i*2,node_labels[i]]] AS nnn, 
  [j IN szr | [j*2+1, rel_types[j]]] AS rrr
UNWIND nnn+rrr AS uls
RETURN uls
ORDER BY uls
```

The output is:
```
[0, ["Person"]]
[1, "ACTED_IN"]
[2, ["Movie"]]
[3, "REVIEWED"]
[4, ["Person"]]
[5, "REVIEWED"]
[6, ["Movie"]]
[7, "ACTED_IN"]
[8, ["Person"]]
```

If you slightly modify the ending (`RETURN uls[1] ORDER BY uls[0]`), then you
can ditch the index we made:
```
["Person"]
"ACTED_IN"
["Movie"]
"REVIEWED"
["Person"]
"REVIEWED"
["Movie"]
"ACTED_IN"
["Person"]
```

Some obvious improvements:
* we don't can't much about the label "Person", but the name of the person
* it would be nice to show the directionality of relationships as well

So, dealing with the first issue was actually kind of easy.  Instead of getting `coalesce(labels(node),'')`,
we get `coalesce(node.name, node.title)`.  Knowing that we are alternating between :Person nodes and :Movie nodes,
we can assume that we care either about node.name or node.title, respectively, which the coalesce() function allows
us to do nicely here.

```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[*..20]-(t:Person {name:"Tom Hanks"}))
WITH extract(node IN nodes(path) | coalesce(node.name,node.title)) AS node_names,
     extract(rel IN relationships(path) | type(rel)) AS rel_types
WITH node_names, rel_types, 
  range(0,size(node_names)-1) AS szn, 
  range(0,size(rel_types)-1) AS szr
WITH [i IN szn | [i*2,node_names[i]]] AS nnn, 
  [j IN szr | [j*2+1, rel_types[j]]] AS rrr
UNWIND nnn+rrr AS uls
RETURN uls[1]
ORDER BY uls[0]
```

This spits out:
```
"Keanu Reeves"
"ACTED_IN"
"The Replacements"
"REVIEWED"
"Jessica Thompson"
"REVIEWED"
"Cloud Atlas"
"ACTED_IN"
"Tom Hanks"
```

Btw, if we don't care about reviewers, we can specify we only want actor or director connections in the
first line of the querylike so:
```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
```

This gives us:
```
"Keanu Reeves"
"ACTED_IN"
"The Matrix Revolutions"
"ACTED_IN"
"Hugo Weaving"
"ACTED_IN"
"Cloud Atlas"
"ACTED_IN"
"Tom Hanks"
```


This doesn't do exactly what I want yet, but it gets us closer: it identifies which are the start and end nodes
of each relationship in the path:
```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS rels
WITH rels,
  [r IN rels | startnode(r)] AS sn,
  [r IN rels | endnode(r)] AS rn
RETURN sn, rels, rn
```

This made me realize that we do not need to know both the startnode and endnode: just the startnode, and
compare it to the nodes from nodes(path).  Like so:
```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS path_edge, nodes(path) as path_node
WITH path_nodes, path_edges,
  [r IN path_edges | startnode(r)] AS edge_starts
WITH [i in range(0,size(path_edges)-1) | CASE WHEN path_nodes[i] = edge_starts[i] THEN '-->' ELSE '<--' END] as directions
RETURN directions

  direction
  ["-->", "<--", "-->", "<--"]
```

Now we just need to combine this logic with that we found above...

```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH extract(node IN nodes(path) | coalesce(node.name,node.title)) AS node_names,
     extract(rel IN relationships(path) | type(rel)) AS rel_types
WITH node_names, rel_types, 
  range(0,size(node_names)-1) AS szn, 
  range(0,size(rel_types)-1) AS szr
WITH [i IN szn | [i*2,node_names[i]]] AS nnn, 
  [j IN szr | [j*2+1, rel_types[j]]] AS rrr
UNWIND nnn+rrr AS uls
RETURN uls[1]
ORDER BY uls[0]
```


```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS path_edges, nodes(path) as path_nodes
WITH path_nodes, path_edges,
  [r IN path_edges | startnode(r)] AS edge_starts,
  extract(node IN path_nodes | coalesce(node.name,node.title)) AS node_names,
  extract(rel IN path_edges | type(rel)) AS rel_types  
WITH node_names, rel_types,
  range(0,size(node_names)-1) AS node_range, 
  range(0,size(rel_types)-1) AS rel_range,
  [i in range(0,size(path_edges)-1) | CASE WHEN path_nodes[i] = edge_starts[i] THEN '-->' ELSE '<--' END] as directions
WITH node_names, rel_types, directions,
  [i IN node_range | [i*2,node_names[i]]] AS nnn, 
  [j IN rel_range | [j*2+1, rel_types[j]+' '+directions[j]]] AS rrr
UNWIND nnn+rrr AS uls
RETURN uls[1]
ORDER BY uls[0]
```

I'm happy to report that we ARE GETTING SOMEWHERE!!!  Here's the output:
```
"Keanu Reeves"
"ACTED_IN"
"-->"
"The Matrix Revolutions"
"ACTED_IN"
"<--"
"Hugo Weaving"
"ACTED_IN"
"-->"
"Cloud Atlas"
"ACTED_IN"
"<--"
"Tom Hanks"
```

This gets a little closer:
```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS path_edges, nodes(path) as path_nodes
WITH path_nodes, path_edges,
  [r IN path_edges | startnode(r)] AS edge_starts,
  extract(node IN path_nodes | coalesce(node.name,node.title)) AS node_names,
  extract(rel IN path_edges | type(rel)) AS rel_types  
WITH node_names, rel_types,
  range(0,size(node_names)-1) AS node_range, 
  range(0,size(rel_types)-1) AS rel_range,
  [i in range(0,size(path_edges)-1) | CASE WHEN path_nodes[i] = edge_starts[i] THEN '-->' ELSE '<--' END] as directions
WITH node_names, rel_types, directions,
  [i IN node_range | [i*2,node_names[i]]] AS nnn, 
  [j IN rel_range | [j*2+1, rel_types[j]+' '+directions[j]] AS rrr
UNWIND nnn+rrr AS uls
WITH uls[1] as ls
ORDER BY uls[0]
WITH collect(ls) as cls
RETURN reduce(str='', item in cls | str+item+' ') as path_string
```
...which returns:
```
path_string
"Keanu Reeves ACTED_IN --> The Matrix Revolutions ACTED_IN <-- Hugo Weaving ACTED_IN --> Cloud Atlas ACTED_IN <-- Tom Hanks "
```

One thing is for sure: doesn't seem like you need the extract() function.  The list
comprehensions seem to do the same thing.  This produces the same output:

```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS path_edges, nodes(path) as path_nodes
WITH path_nodes, path_edges,
  [r IN path_edges | startnode(r)] AS edge_starts,
  [node IN path_nodes | coalesce(node.name,node.title)] AS node_names,
  [rel IN path_edges | type(rel)] AS rel_types  
WITH node_names, rel_types,
  range(0,size(node_names)-1) AS node_range, 
  range(0,size(rel_types)-1) AS rel_range,
  [i in range(0,size(path_edges)-1) | CASE WHEN path_nodes[i] = edge_starts[i] THEN '-->' ELSE '<--' END] as directions
WITH node_names, rel_types, directions,
  [i IN node_range | [i*2,node_names[i]]] AS nnn, 
  [j IN rel_range | [j*2+1, rel_types[j]+' '+directions[j]]] AS rrr
UNWIND nnn+rrr AS uls
WITH uls[1] AS ls
ORDER BY uls[0]
WITH collect(ls) AS cls
RETURN reduce(str='', item in cls | str+item+' ') AS path_string
```

I think the key is is combining the arrows and rel_types in the CASE statement.  Two of the WITH
statements can actually be cleaned up and combined into one as well...

```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS path_edges, nodes(path) as path_nodes
WITH path_nodes, path_edges,
  [r IN path_edges | startnode(r)] AS edge_starts,
  [node IN path_nodes | coalesce(node.name,node.title)] AS node_names,
  [rel IN path_edges | type(rel)] AS rel_types  
WITH node_names, rel_types,
  [i IN range(0,size(node_names)-1) | [i*2,node_names[i]]] AS nnn, 
  [i in range(0,size(path_edges)-1) | CASE 
    WHEN path_nodes[i] = edge_starts[i] THEN [i*2+1, '-[:'+rel_types[i]+']->']
    ELSE [i*2+1, '<-[:'+rel_types[i]+']-'] END] as rel_type_directions
UNWIND nnn+rel_type_directions AS uls
WITH uls[1] AS ls
ORDER BY uls[0]
WITH collect(ls) AS cls
RETURN reduce(str='', item in cls | str+item+' ') AS path_string

  path_string
  "Keanu Reeves -[:ACTED_IN]-> The Matrix Revolutions <-[:ACTED_IN]- Hugo Weaving -[:ACTED_IN]-> Cloud Atlas <-[:ACTED_IN]- Tom Hanks "
```

NICE!!!

If you want to return this as a list instead of a single string:
```
MATCH path = shortestPath((k:Person {name:"Keanu Reeves"})-[:ACTED_IN|DIRECTED*..20]-(t:Person {name:"Tom Hanks"}))
WITH relationships(path) AS path_edges, nodes(path) as path_nodes
WITH path_nodes, path_edges,
  [r IN path_edges | startnode(r)] AS edge_starts,
  [node IN path_nodes | coalesce(node.name,node.title)] AS node_names,
  [rel IN path_edges | type(rel)] AS rel_types  
WITH node_names, rel_types,
  [i IN range(0,size(node_names)-1) | [i*2,node_names[i]]] AS nnn, 
  [i in range(0,size(path_edges)-1) | CASE 
    WHEN path_nodes[i] = edge_starts[i] THEN [i*2+1, '-[:'+rel_types[i]+']->']
    ELSE [i*2+1, '<-[:'+rel_types[i]+']-'] END] as rel_type_directions
UNWIND nnn+rel_type_directions AS uls
WITH uls[1] AS ls
ORDER BY uls[0]
RETURN collect(ls) AS path_list

path_list
["Keanu Reeves", "-[:ACTED_IN]->", "The Matrix Revolutions", "<-[:ACTED_IN]-", "Hugo Weaving", "-[:ACTED_IN]->", "Cloud Atlas", "<-[:ACTED_IN]-", "Tom Hanks"]
```

El Fin... Or is it?

To generalize this, there is one weak point: the coalesce function that assumes only two node types
in the traversal.  What if there are three nodes types, or more?

My hack would be to nest coalesce statements:
```
coalesce(coalesce(node.name, node.kittycat), node.title)
```

It ain't beautiful, but it will work!  

#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!#!


Various ways to set/remove things on a node
```
MERGE (p:Person {name: "Kevin Suburban"}) // create node if not yet created
SET p.thirsty = true, p.hungry = false // by .property
SET p += {thirsty: false, hungry: true, bottles_of_wine: 1} // mutation
SET p:DataScientist:Physicist:Punk // add labels
REMOVE p.bottles_of_wine, p:Punk
RETURN p {.*, labels: labels(p)}

{
  "name": "Kevin Suburban",
  "hungry": true,
  "thirsty": false,
  "labels": [
    "Person",
    "DataScientist",
    "Physicist"
  ]
}
```

How to set something programmatically on a whole set of labeled nodes:
```
MATCH (p:Person)
WITH p, collect(p) as p_list
FOREACH (x in p_list | SET x.first_initial = substring(x.name,0,1))
```

Interesting behavioral note: say you wanted to do that and return a few 
examples to see if it was working as planned.  Naively, you might do this:
```
MATCH (p:Person)
WITH p, collect(p) as p_list
FOREACH (x in p_list | SET x.second_initial = substring(split(x.name,' ')[1],0,1))
RETURN p LIMIT 1
```

However, unfortunately the (MATCH, RETURN) block has evaluated within the same
scope (am I using that lingo right here? think so -- moving on!).  What I mean is, Cypher
tells Neo4j, "Look for all people until you find 1 person, then put that person in a list
and for each person in that list, set a new property."  

If you must return something when executing this job, then put the RETURN statement into
a new scope/block using WITH and UNWIND:
```
MATCH (p:Person)
WITH collect(p) as p_list
FOREACH (x in p_list | SET x.second_initial = substring(split(x.name,' ')[1],0,1))
WITH p_list UNWIND p_list as p
RETURN p limit 1
```



