
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

Pretty sure this answers my question about the ability to give Nodes a JSON structure... :-(

The Node is flat...

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
