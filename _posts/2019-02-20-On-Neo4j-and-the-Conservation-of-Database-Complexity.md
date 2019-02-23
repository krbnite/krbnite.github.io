---
layout: post
title: On Neo4j and the Conservation of Database Complexity
tags: neo4j databases
---

There is a lot to love about Neo4j, especially if you are modeling data that has a high relationship density
and highly heterogenous relationship types, patterns, and structures.  

Social networks are often used as a prime example here
because they're familiar and easy to motivate graph databases in general.  If we think in terms of a "Person" table,
then one can imagine all kinds of zany, heirarchical, and recursive relationships amongst its rows.  The diversity in
the patterns of relationships is matched by the sheer number of relationship types that may exist.  Do you
create a bazillion bridge tables?  Do you denormalize like crazy?  From a database user's perspective, the SQL
queries can get truly unruly (whoa, nice rhyme!).  From an internal perspective, excessive table scans
can drive down the performance.  

The major sell of Neo4j is getting rid of all this icky complexity... But in this post, I ask: have they
been successful?  Or have they merely shifted where the complexity resides?  After several months of 
tinkering, I wonder if there some fundamental conservation law at play.  Is database complexity like a game
of Wack-a-Mole?  

**Word of Warning**:  I'm a STEM mutt, not a pure bred DBA.  Tread carefully.

**Note**: I realize it can sound like I'm coming down on Neo4j in this post, but I think it should
be interpreted as growing pains along the learning curve.  I'm still going forward with Neo4j on
my project at work, though I am happy about a few things that help make a change of course somewhere down
the line possible:
* OpenCypher has open sourced Neo4j's query language, Cypher
  - other graph databases have already adopted it (e.g., 
  [AgensGraph](https://github.com/bitnine-oss/agensgraph) and 
  [RedisGraph](https://oss.redislabs.com/redisgraph/)) 
* Cypher-to-Gremlin translators exist
  - Gremlin is the graph language that some other graph databases use (e.g., 
  [TinkerPop](http://tinkerpop.apache.org/)
  and [AWS Neptune](https://docs.aws.amazon.com/neptune/latest/userguide/intro.html))
  - e.g., [Cypher for Gremlin](https://github.com/opencypher/cypher-for-gremlin) not only provides a
  translator, but generally expands Gremlin to include Cypher


# I don't think that query means what you think it means
Joins get a lot of flak from NoSQL folks.  Well, maybe not the simple joins -- i.e., joins that are just "one join 
deep."  But "analytical" joins certainly do -- you know, inception joins (joins within joins within joins) that a
data scientist might string together to generate an insightful data product.  There
have certainly been times where I wonder if a complex query is doing what I think it's doing, which can require
careful analysis of the query and the result set. 

Graph databases like Neo4j proclaim freedom from this nightmare, but I've also experienced a similar 
pit in my stomach with some simple Cypher queries.

For example: be careful with MERGE.  I know I've written about this before, but I have found it to be easy
to slip into query patterns that don't do what you think they're doing.  

For example, say you created a "Tool" node and "Job" node like so:
```
CREATE (:Tool {name: "Hammer"});
CREATE (:Job {name: "Build Backyard Deck"});
```

Then, what do you think this query does:
```
MERGE (job:Job {name: "Build Backyard Deck"})-[:REQUIRES]->(:Tool {name: "Hammer"});
```

Answer:  It looked to see if this pattern existed in the database.  It didn't.  So
this pattern was created -- from scratch.  Importantly, it didn't  look to see if parts of
the pattern existed, then only create what was needed.  You now have two "Tool" nodes named
"Hammer" and two "Job" nodes named "Build Backyard Deck".  

What you have to do if first MATCH on the nodes that you've already CREATED and will be using in the pattern, assign
them a variable name, then MERGE against the pattern.  Neo4j will know not to create the nodes
that it already matched against:
```
MATCH (tool:Tool {name: "Hammer"})
MATCH (job:Job {name: "Build Backyard Deck"})
MERGE (job)-[:REQUIRES]->(tool);
```

Moral:  Remember that MERGE means "MATCH OR CREATE" and that it operates against an entire pattern at once.

In this particular case, we could do away with MATCH and CREATE statments altogether:
```
MERGE (tool:Tool {name: "Hammer"})  // MATCH or CREATE this :Tool node
MERGE (job:Job {name: "Build Backyard Deck"})  // MATCH or CREATE this :Job node
MERGE (job)-[:REQUIRES]->(tool)  // MATCH or CREATE this :REQUIRES relationship
```

That all said, sometimes you don't want that behavior.  Sometimes you do want to create additional
nodes.  And if confusion at the syntactic level did not motivate it enough, I think this is really
where the hypothesis for "conservation of database complexity" comes in.  It all has to do with
keys and constraints, or the lackthereof.


# Are Keys and Constraints More or Less Complex?

In a relational database, a table has a primary key -- a unique way of identifying a row,
which represents some tuple of information representing some object.  Sometimes there is a column
that naturally lends itself to this, but more often than not (in the databases I've used) an ID column 
is used to ensure uniqueness.  Other times, a table's primary key is composite.  Point is, the idea of 
including IDs to ensure unique rows is pretty common...  And consistent.  Normalizing a database, 
or deciding on a denormalized
solution, can be daunting if you require hordes of foreign keys and bridge tables, but it's a battle that has been
fought and won many times over.  It's routine.  The complexity is understood and documented.

*\<SalesmanTone>  With Neo4j, you can develop at the speed of thought: your whiteboard 
model is your logical model.  No need to deal with all those bridge tables and foreign keys: throw 
'em away and get rid of that nasty complexity!  In Neo4j, relationships are first-class citizens.
Need a new relationship between entities?  No worries:  just do it!  No need to create a new bridge 
table.  In fact, no need to do anything at all: this is schema optional, pedal-to-floor development, 
baby!\<endSalesmanTone>*


It seems so simple, but from this simplicity, complexity seeps in.  For example, 
making things up as you go along is awesome until it's not.  In fact, this is not really even a dig
at Neo4j: they will be the first ones to tell you that "schema optional" doesn't mean you shouldn't 
design a great schema.  It's optional, and you should opt to do it!

It is because of the flexibility and the lack of constraints that you might find yourself
reinventing standard features of the relational wheel at the application layer.  For example,
check constraints:  Neo4j will not enforce any rule about what values a property of a node may take on
(note that a property of a node is the graph version of a column in a table) . In fact, Neo4j will not even 
ensure that a property of a node will always be of the same data type, like integer or string.  If
you want these types of constraints, you have enforce them  yourself at the application layer(s).
  

This is touching on what I mean by conservation of complexity: someone somewhere is going to deal
with it.  That may be the database administrator, the application designer, or the data scientist
at the end of the pipeline.  

# More on MERGE and Bridge Tables
## The Relational Storyline
In a relational database, you will might have table T1 and table T2, both having their own primary
key (column with ensured uniqueness).  If the objects (rows) in T1 have a many-to-many relationship
with the objects in T2, then a bridge table TB must be created such that T1-TB and T2-TB both have
many-to-one relationships.  The primary key of the bridge table is a composite key of the foreign
keys from T1 and T2.  

Let's make this less abstract.

Let T1 be a table called ArticleOfClothing.  And let T2 be a table called Color.  We know that
the relationship between clothes and color is many-to-many because (i) "jeans" may come in many
different colors, and (ii) "blue" might be the color of many different articles of clothing.

```
# Many-to-Many Relationship
 ___________________            _____________
| ArticleOfClothing |          | Color       |
|-------------------| \      / |-------------|
| a1 | Jeans        | - --- -  | c1 | Red    |
| a2 | T-Shirt      | /      \ | c1 | Green  |
| a3 | Socks        |          | c1 | Blue   |
 -------------------            -------------
```

To set up a relationship between tables, we want to use a foreign key...but we can't.  That is, what
color key should we put in the a1 row of ArticleOfClothing?  We already said that an article of clothing
can take on any color, so there is no single choice.  This is where accidental denormalization can 
occur, i.e., a design choice here can easily break a Normal Form (e.g., using two foreign key columns 
to represent color choices in the ArticleOfClothing table, or using multiple rows and thus repeating
the article name).  The proper thing to do here is to resolve the many-to-many relationship by creating
a bridge table.

```
 # (One-to-Many) BRIDGE (Many-to-One)
  ___________________        _____________         _____________
| ArticleOfClothing |       | AoCHasColor |       | Color       |
|-------------------| \     |-------------|     / |-------------|
| a1 | Jeans        | - --- |  a1 | c1    | ----  | c1 | Red    |
| a2 | T-Shirt      | /     |  a1 | c2    |     \ | c1 | Green  |
| a3 | Socks        |       |  a2 | c2    |       | c1 | Blue   |
 -------------------        |  a2 | c3    |        -------------
                            |  a3 | c1    |
                             -------------
```

Now we can maintain a "clothing dictionary", a "color dictionary", and a clothing/color relationship table.  We
can be assured that (i) any article of clothing will be uniquely represented, (ii) any color will be uniquely
represented, and (iii) and clothing/color combo will be uniquely represented.  

## The Property Graph Storyline
In a property graph (at least in Neo4j), you will might have label L1 and label L2, each having a property
that is constrained to be unique (one of the few constraints in Neo4j Community Edition).  If the objects (nodes) 
labeled L1 have a many-to-many relationship with the objects labeled L2, then we just create all those relationships.

That is, we do not have to **explicitly** create a bridge table. 

Emphasis on explicitly because something akin to a bridge table is implicitly being built each time a new relationship
is created between an L1 node and an L2 node.  Our graph database still has a "bridge collection," which constitutes
the set of relationships between labels L1 and L2.  At the storage level, this is not a bridge table (see: index-free
adjacency).  And at the data retrieval level, this is not a bridge table (index-free adjaceny basically means that
"bridge table scans" are no longer necessary -- just follow the path).  However, at some fuzzy level of reality,
we still have a "bridge table" look-alike -- the "bridge collection". 

In fact, the "bridge collection" looks very much like a bridge table when presented tabularly, which I'll show
in just a moment.

First:  Let's make this less abstract.

Let L1 be a label called :ArticleOfClothing.  And let L2 be a label called :Color.  We know that
the relationship between the nodes of these labels is many-to-many because (i) "jeans" may come in many
different colors, and (ii) "blue" might be the color of many different articles of clothing.  However,
we do not create a bridge table this time:  we just create a relationship called :HAS_COLOR.

In place of primary keys, we create Label(property) uniqueness constraints.
```
CREATE CONSTRAINT ON (a:ArticleOfClothing) ASSERT a.name is UNIQUE;
CREATE CONSTRAINT ON (a:Color) ASSERT a.name is UNIQUE;
```

Note that there is no bridge table with a unique composite of foreign keys.  There is nothing
preventing duplicated relationships in the database (that onus lies on you).  So we could
easily have something like:

```
(:ArticleOfClothing {name: "Jeans"}) -[:HAS_COLOR]-> (:Color {name: "Red"})
(:ArticleOfClothing {name: "Jeans"}) -[:HAS_COLOR]-> (:Color {name: "Green"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "Green"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "Green"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "Blue"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "Blue"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "Blue"})
(:ArticleOfClothing {name: "Socks"}) -[:HAS_COLOR]-> (:Color {name: "Red"})
```

Or in a more relational looking form:
```
 ____________________       __________________        ________
| :ArticleOfClothing |     | :HAS_COLOR       |      | :Color |
|--------------------|     |------------------|      |--------|
| Jeans              | --- |  Jeans   | Red   | ---> | Red    |
| T-Shirt            |     |  Jeans   | Green |      | Green  |
| Socks              |     |  T-Shirt | Green |      | Blue   |
 --------------------      |  T-Shirt | Green |       --------
                           |  T-Shirt | Blue  |
                           |  T-Shirt | Blue  |
                           |  T-Shirt | Blue  |
                           |  Socks   | Red   |
                            ------------------
```

Though we did not specify any properties for the :HAS_COLOR relationship between any nodes,
there are always 2 implicit properties: the starting node and the ending node!  In the above :HAS_COLOR "bridge collection",
we see that there is nothing preventing this spazzy behavior...except for you.

Since you cannot use check constraints in Neo4j, the situation is actually even more dire.  We could
easily have something like this:

```
(:ArticleOfClothing {name: "Jeans"}) -[:HAS_COLOR]-> (:Color {name: "Red"})
(:ArticleOfClothing {name: "Jens"}) -[:HAS_COLOR]-> (:Color {name: "Green"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "Green"})
(:ArticleOfClothing {name: "T-Shirt"}) -[:HAS_COLOR]-> (:Color {name: "gr33n"})
(:ArticleOfClothing {name: "tshirt"}) -[:HAS_COLOR]-> (:Color {name: "blue"})
(:ArticleOfClothing {name: "tshirt"}) -[:HAS_COLOR]-> (:Color {name: "blue"})
(:ArticleOfClothing {name: "tshirt"}) -[:HAS_COLOR]-> (:Color {name: "BLUE"})
(:ArticleOfClothing {name: "Socks"}) -[:HAS_COLOR]-> (:Color {name: "red"})
(:ArticleOfClothing {name: "SOCKS"}) -[:HAS_COLOR]-> (:Color {name: "red"})
```

Or in a more relational looking form:
```
 ____________________       __________________        ________
| :ArticleOfClothing |     | :HAS_COLOR       |      | :Color |
|--------------------|     |------------------|      |--------|
| Jeans              | --- |  Jeans   | Red   | ---> | Red    |
| Jens               |     |  Jens    | Green |      | Green  |
| T-Shirt            |     |  T-Shirt | Green |      | Blue   |
| tshirt             |     |  T-Shirt | gr33n |       --------
| Socks              |     |  tshirt  | blue  |
| SOCKS              |     |  tshirt  | blue  |
 --------------------      |  tshirt  | BLUE  |
                           |  Socks   | red   |
                           |  SOCKS   | red   |
                            ------------------
```

The uniqueness constraints on the labels was not enough to prevent incorrect values to be
added to the "Label Tables".  The lack of foreign keys in a bridge table prevented unique
relationships, e.g., (tshirt, blue) comes up twice.  

"Ok, so what?" you may be asking.  "Let's assume you're not an idiot."

Ok, ok -- let's assume that we do not accidentally use incorrect values.  This is an area where really understanding
MERGE comes into play.  Using it correctly will at least prevent duplicate relationships (to a certain extent).

I've run into another scenario or two where I've scratched my head... Basically, on the importance of really
knowing your Cypher.  For example, say you want to MATCH something, then update it:
* If you use MERGE and it doesn't exist, it will be created and updated
* If you use MERGE and it does exist, it will just be updated
* If you use MATCH (followed by a SET command) and its exists, it will be updated
* **But if you use MATCH (followed by a SET command) and it doesn't exist, nothing will be
updated and you will not be notified.**  There is no error or hiccup of any kind!

I could go on... For example, another article entirely might focus on the differences
between Neo4j Community Edition and Enterprise Edition -- any why you basically need Enterprise
for anything beyond playing around (e.g., for role-based access control, subgraph access control,
and better security features).  But I'm getting tired of writing at this point!  So let's move on to 
a moral already!

# Moral (?)
I guess it's true for any language or database technology, but you really have to know what you're 
doing.  Using Cypher and Neo4j, you have to ensure many things yourself that you might take for granted in, say, Postgres or 
MySQL.  In these relational settings, you just have to learn enough to set up the constraints when
you're building the table schema... Then you basically don't have to worry about it.  In Neo4j, you
can set up some constraints, but for the most part the schema is all in your head!  Sure, you can build an app 
or script on top that ensures constraints, data integrity, and so on...but -- c'mon! -- that's
really not always the case. 

Bottom line is:  you have to promise yourself to not fuck it up!



