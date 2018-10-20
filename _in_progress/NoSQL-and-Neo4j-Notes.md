
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

