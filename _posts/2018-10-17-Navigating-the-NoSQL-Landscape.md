---
title: Navigating the NoSQL Landscape
layout: post
tags: database nosql easi
---

At work, we want to have a database detailing various wearables devices and home sensors.  In this database,
we would want to track things like the device's name, its creator/manufacturer, what sensors it has, what
data streams it provides (raw sensor data? derived data products? both?), what biological quantities it
purports to measure (e.g., heart rate, heart rate variability, electrodermal activity), whether or not its
claims have been verified/validated/tested, whether or not its been used in published articles, and so on.  For
much of this, a standard relational database would be just fine (though there can be some tree-like or recursive
relationships that begin to crop up when mapping raw sensor data to derived data streams).  

Our idea is a bit grander, however: ultimately, it would be nice to map devices to diseases in nontrivial 
ways.  Even for something simple like heart rate variability, we want to show something like

```
[:Device D1] -:HAS-> [:Sensor S1] -:EMITS-> [:DataStream DS1] -:FEEDS_INTO-> [:Algorithm A1] 
             \                                                                ^
              \ -:HAS-> [:Sensor S2] -:EMITS-> [:DataStream DS2] -:FEED_INTO--|

[:Algorithm A1] -:SPITS_OUT-> [:DataStream DS3] -:IS_PROXY_FOR-> [:Phenomenon P1] 

[:Phenomenon P1] <-:IS_CONDITION_OF- [:Phenomenon P2] -:IS_INDICATIVE_OF-> [:Phenomenon P3]

[:DataStream DS3] -HAS_DATA_SIGNATURE-> [:DataSignature:DataStream Sig1] -:IS_PROXY_FOR-> [:Phenomenon P2]

[:DataStream DS3] -FEEDS_INTO-> [:Algorithm A2] -:Spits_Out-> [:DataSignature:DataStream Sig1]
```

It can get more complicated when mixing and matching devices, and when looking for multiple,
simultaneous data signatures.  

What's most important to note is all the "recursive" relationships
going on.  If we just had a data streams table in a relational database, each
relationship would be a join table.   Above, we show that mapping from a device to a biological
phenomenon could take a path through multiple data streams, requiring a tower of
JOIN statements in a SQL query.  Further more, each JOIN represents another table scan, which
can be time consuming for a large database.  One might think to have a "data stream"
table and "derived data stream" table, but this just pushes the problem to
the "derived data stream" table since one often takes multiple raw and derived
data stream to create yet another derived data stream. 

This same issue arises with biological phenomena.  In fact, I ultimately settled on the
word "phenomena" to simplify matters after realizing having tables for "symptoms" and
"diseases/conditions" didn't makes sense -- sometimes a disease is a symptom of another disease, etc.  

This led me on a search through the NoSQL netherrealm, where I quickly learned a few things:
* a Key-Value (KV) store was definitely way too structureless and simple for our needs
* a column-"buzzword" database has some ambiguity in its definition
  - one kind has the look and feel of a regular ol' relational database, but it optimized by column (not record)
  - the other has the look and feel of some mish-mash of a KV or document store (again though, having to do with how its
    optimized in the backend)
* a document store could have potential if jerry-rigged properly, but this would basically amount
  to superficially mimicking a graph database
* the flexible connectivity of a graph database can easily handle complicated relationships: 
  relational/tabular, to tree/document-like, to recursive, to any graph-like doo-hickey ding-a-ling doo-dad
* for no other reason than colorful relationships and complex connectivity do we need something
  like a graph database or NoSQL solution for this particular project (at least at this stage in
  the game) since it is not intended to serve thousands of users simultaneously, mild latency issues
  isn't a real problem, and so on

I ultimately found that a native graph database
like Neo4j solves both problems at once (despite only the first really mattering for my
use case): the query would require maybe 1-2 lines of 
Cypher code and the "table scan" is replaced by a local graph structure.  The real
key is Cypher, I think, but also the flexibility given by a graph database structure.  There
are issues, of course, like a lack of constraints.

But there were many other contenders that I may even come back to.  For example, Postgres is a "relational
database," yet supports KV/document functionality.  With AgensGraph, Postgres even becomes graph-like.  Apparently,
the issue of horizontally scaling a database cluster still holds, meaning Postgres would not be
considered truly in the spirit of "NoSQL", but not every project needs horizontal scale out (e.g., mine doesn't!).

Anyway, I digress!

What I wanted to do here was leave a paper trail of the various things I learned and
notes I took along the path of better understanding the "NoSQL" landscape.

------------------------------------------------------


This [Data Store Comparison](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-comparison) 
from Microsoft is worth many reads.  

Think of needs:
* does you DB just need to find info on a document to quickly write to or read from, where the document might not easily relate to other documents, but also -- the use case doesn't call for it
* or, maybe your DB needs to relate data across many different entities and business verticals, very quickly:  how quick do you need joining, searching, and pattern matching to be?
* does it need to retrieve and sort (order by) quickly
* Do relationship really matter?  Would visualizing relationships quickly uncover insights
  without the need for long, tediuos, and/or complex analytical queries?
  - e.g., an example use-case for graph databases from AWS Database Week (Oct 2018) is fraud detection.  Consider
    Uber: how many times in NYC would you expect Driver X and Customer Y to interact? Once, maybe twice.  What if there 
    are 25 edges between them?  Might be fraud!
  
  



# A Little History


Notes on the "Non-Relational Revolution" from AWS Database Week: NoSQL & Graph Databases (Oct 17, 2018):
* Werner Vogels' web blog (CTO of Amz) on building scalable and robust distributed systems
* Timeline
  - late 70s: Oracle
  - early 80s: DB2
  - late 80s: SQL Server
  - early 90s: MySQL
  - late 90s: PostgreSQL
  - late 2000s: Cassandra, MongoDB, Redis
  - early 2010s: Elastisearch, DynamobDB, Redshift
  - late 2010s: Aurora, Neptune
  - note that the list is AWS-heavy at the end there only b/c they don't list alternatives / all major DBs (think Google's BigTable, Neo4j, etc)
* What changed
  - Concurrent users: Superbowl had over 5 million concurrent users
  - Global distribution of users
  - Data volume: from TBs to PBs, and even EBs
  - Can't just handle extremely high concurrency and volume: still need to perform!
* and performance expectations from users only increase (e.g., ms or microsecond)
* All these changes made people question the "one tool" approach to databases (wouldn't use a hammer for sawing a board)
  - e.g., what if each record does not care about any other record?  (no need to scan a whole table, maybe use KV)
  - e.g., what if each instance of a full entity doesn't care about other entities?  (no need to scan and join tables, maybe use a docStore)





# Relational Database

From [PluralSight](https://www.pluralsight.com/blog/software-development/relational-non-relational-databases)
* "The structure of a relational database allows you to link information from different tables through the use of 
  foreign keys (or indexes), which are used to uniquely identify any atomic piece of data within that table. Other 
  tables may refer to that foreign key, so as to create a link between their data pieces and the piece pointed to by 
  the foreign key. This comes in handy for applications that are heavy into data analysis."
* "If you want your application to handle a lot of complicated querying, database transactions and routine analysis 
  of data, you’ll probably want to stick with a relational database. And if your application is going to focus on doing 
  many database transactions, it’s important that those transactions are processed reliably. This is where ACID (the set 
  of properties that guarantee database transactions are processed reliably) really matters, and where referential 
  integrity comes into play."
* "A non-relational database just stores data without explicit and structured mechanisms to link data from different 
  tables (or buckets) to one another."
 
 
[PluralSight](https://www.pluralsight.com/blog/software-development/relational-non-relational-databases) goes on to list some reasons why one might choose a NoSQL solution:
* The need to store serialized arrays in JSON objects 
* Storing records in the same collection that have different fields or attributes 
* Finding yourself de-normalizing your database schema or coding around performance and horizontal scalability issues 
* Problems easily pre-defining your schema because of the nature of your data model

And a reason why not:
* "In non-relational databases like Mongo, there are no joins like there would be in relational databases. This 
  means you need to perform multiple queries and join the data manually within your code -- and that can get very 
  ugly, very fast."
  
From [Microsoft's Data Store Overview](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview):
* Relational (tabular) databases organize data as a series of two-dimensional tables with rows and columns. 
* The query language is almost always a dialect of SQL
* Many RDBMSs offer ACID-compliant transactions for updating information
* Schema is specified ahead of time, and all read/write operations respect the schema
* Strong consistency guarantees
* Tabularized information is put into relational structure by the normalization process, which can be complex and unintuitive when querying




# Key-Value (KV) and Document Stores

I could be wrong, but KV and document stores seem to have basically come about due to the needs and gripes of 
JavaScript-heavy app developers.  A front-end developer uses JSON objects to push around data for this and 
that, and the idea was "Why not also push JSON to the back-end as well?"  The other idea was, "Why SQL when 
we can just keep using JavaScript?"  Thus the MEAN stack.  This way you push and pull the data as JSON objects, but 
lose out on joins... But if you don't need joins, then who cares?  Not the devs who pushed for document stores: you 
can technically join data in your app code.  You might miss out on optimization, constraints, and integrity 
this way -- up to you and your own talents, I suppose.  One of the complaints is object-relational impedance 
mismatching, which I understand to have to do w/ the complexity in object-relational mapping, and the associated 
latency.  Anyway, point is, they be like: "Why no just keep in JS?!?!"

There is some truth to this:  Remember, NoSQL solutions began cropping up and "became popular with the introduction 
of the web, when databases went from a max of a few hundred users on an internal company application to thousands or 
millions of users on a web application," says [James Serra](https://www.jamesserra.com/archive/2015/08/relational-databases-vs-non-relational-databases/))
in a blog article mostly discussing document and KV stores.  An article on [Dataversity](http://www.dataversity.net/a-brief-history-of-non-relational-databases/) echoes this web-centric origin: 
"In the mid-1990s, the internet gained extreme popularity, and relational databases simply could not keep up with the
flow of information demanded by users, as well as the larger variety of data types that occurred from this 
evolution. This led to the development of non-relational databases, often referred to as NoSQL. NoSQL databases 
can translate strange data quickly and avoid the rigidity of SQL by replacing 'organized' storage with more 
flexibility."

For this better flexibility, faster processing, and higher availability, NoSQL solutions abandon
ACID compliance in favor of BASE.  "The strength of ACID is the guarantee it will provide a safe environment for 
processing data. This means data is consistent and stable," says
[Dataversity](http://www.dataversity.net/a-brief-history-of-non-relational-databases/).  "Availability 
for scaling purposes is an important feature for BASE data 
stores," which includes column, document, and key value stores. "However, it doesn’t offer the guarantee of 
consistency for replicated data during write time. The BASE
model, generally speaking, provides less assurance than ACID."  For banking, this is a problem -- for that,
go with ACID!  But for many other activities, it's much less of a problem, or not an issue at all.  "The highest 
priority for web-based applications is the ability to service large numbers of user requests, which is the 
strength of non-relational databases."  For example, the eBay webpage is more concerned with "quick response 
time and wants to assure fast page loading, rather than enforcing strict business rules."


[James Serra](https://www.jamesserra.com/archive/2015/08/relational-databases-vs-non-relational-databases/): 
"The bottom line for using a [document or KV] solution is if you have an OLTP application that has thousands of users and 
has a very large database requiring a scale-out solution and/or is using JSON data, in particular if this JSON 
data has various structures. You also get the benefit of high availability as NoSQL solutions store multiple 
copies of the data. Just keep in mind for performance you may sacrifice data consistency, as well as the 
ability to join data, use SQL, and to do quick mass updates." 

[Dataversity](): 
"While the non-relational database has certain strengths when storing data, it also comes with a significant drawback – 
key-value stores cannot 'enforce' the relationships between items. ...the relational model comes with a built-in, 
foolproof way of ensuring business logic and trustworthiness at the database layer. ... This is why relational 
databases continue to be popular."


## Key-Value (KV) Stores

From [Microsoft's Data Store Overview](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview):
* A key/value store is basically a large hash table (hashing function helps distribute data evenly across system)
* Most only support simple query, insert, and delete operations
* The stored values are opaque to the storage system software (values are essentially blobs)
* Any schema information must be provided and interpreted by the application
* highly optimized for applications performing simple lookups
* not suitable for systems that need to query data across different key/value stores
* not optimized for scenarios where querying by value is important (i.e., no WHERE clause; lookups are based only on keys!)
* extremely scalable due to its complete lack of relationships between KV pairs (can easily distribute data across multiple nodes on separate machines)
* not typically used for data that requires updates, which are basically all-or-none (full rewrites)

## Document Stores
From [Microsoft's Data Store Overview](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview):
* conceptually similar to a key/value store, except that its values are documents, not "blobs"
* a "document" may be encoded in XML, YAML, JSON, BSON, or even stored as plain text
* similar to a KVS, these documents can be accessed by key (often hashed to help distribute data evenly) 
* however, documents are not opaque to the storage management system (unlike the "blobs" of KV stores), so one is also able query and filter data by using the values in a document's fields
* documents are usually entity-oriented, i.e., a single document contains the entire data for an entity (unlike in a relational DB, where this info would be distributed across many tables)
* documents of the same type need not all have the same structure (flexible schema)
* some document stores support indexing to facilitate fast lookups based on one or more fields
* can update fields in a document w/o rewriting entire document (unlike a KVS blob, which is basically an all-or-none update)


# In-Memory (or Cache) Databases

I see "In-Memory" listed as a NoSQL class every so often.  Maybe it is!  Though to me it seems more like a 
database/datastore feature feature than its own category.  For example, Redis is considered to be an in-memory 
database, but you will also see it referred to as a KV store.  In fact, it's both!  One adjective ("in-memory")
refers to how the technology is operationalized (where it lives), while the other adjective ("key-value") refers
to its data structure.  (Elsewhere in these notes, I cover a similar classification issue with Redshift: 
is it column-oriented or relational?  Both!)


Notes on "Amazon ElastiCache" from AWS Database Week: NoSQL & Graph Databases (Oct 17, 2018):
* internet-scale (global) apps need low latency (milli- to micro-seconds) and high concurrency (1M+ users)
- can require high volume storage (TB, PB, EB), high request rate (M/s), access from various sources (mobile, IoT, devices), etc, etc
* approaches to reduce latency
- in-memory databases and data grids
- specialized hardware such as multi-core processors, GPUs, accelerators
* requires specialized hardware and software skills
- data reduction approaches, such as sampling, aggregation
* ElastiCache
- fully-managed, Redis or Memcached compatible, low-latency, in-memory data store
- so basically, AMZ lets you pay to use open source cache DBs
* EastiCache Redis
- fast, fully-managed, in-mem, cloud-based data store often used as a db, cache, message broker, and/or queue 
- highly available: read replicas, multiple primaries, multi-AZ w/ auto failover
- secure and compliant (e.g., HIPAA compliant)
- highly scalable (obviously... Redis is KV, right? And just increase memory and nodes...)


# Column Family

From [Microsoft's Data Store Overview](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview):
* organizes data into rows and columns (has some appearances of a traditional relational database)
* implements a powerful, denormalized approach to structuring sparse data 
* columns are split into logically-related groups called "column families" for columns that are typically retrieved or manipulated as a unit


Here is an [interesting article from ByteContinuum](http://bytecontinnum.com/2016/07/column-stores-column-oriented-databases/) 
on the difference between a column-oriented database (very similar to traditional, 
but column-oriented) and a general "column store".  Basically, both use the idea of "column family" .... Example 
given: BigTable, HBase, and Cassandra are often called "column-oriented databases" just because they use "column 
families" He (and others) point out that it is very misleading to refer to these as "column-oriented".   This article 
was a refreshing because only moments before reading it, I was having this strange feeling that "column stores" seem 
to be inconsistently described... Pointing out that almost everyone incorrectly conflates a bunch of things into 
"column oriented" was helpful....  There are other articles on this:
* [Is Cassandra a Column-Oriented or Columnar Database?](https://stackoverflow.com/questions/25441921/is-cassandra-a-column-oriented-or-columnar-database)
* [Why many refer to Cassandra as a Column oriented database?](https://stackoverflow.com/questions/13010225/why-many-refer-to-cassandra-as-a-column-oriented-database)
* [Distinguishing Two Major Types of Column-Stores](http://dbmsmusings.blogspot.com/2010/03/distinguishing-two-major-types-of_29.html)

One of the questions that led me to this was wondering, "What is Redshift?"  I've often read that it's some kind
of columnar or column-oriented database.  However, at AWS Database Week (Oct 2018), one of the speakers from 
AWS referred to it as relational!  Fact is, it's both!  One adjective (columnar) refers to how the technology
is optimized, while the other (relational) refers to its data structure and query language.  How you refer to
Redshift (or any datastore) stems from what parameter or attribute you are categorizing at the moment.  (By the
way, this is also part of the reason why terms like "NoSQL" and "non-relational" only have fuzzy meaning out
of specific contexts.  Like, Postgres is a dyed-in-the-wool relational database, right?  Yet it supports 
key-value and document-store data structures, making it also non-relational and NoSQL.  Etc.)

- even AWS sells Redshift as "relational", so it's interesting: we've come to a point in time where we are no longer strictly delineating databases by how they work under the hood


* https://docs.aws.amazon.com/redshift/latest/dg/c_columnar_storage_disk_mem_mgmnt.html
  - Redshift is considered a RDBMS, but is a column-oriented database, which make it read-optimized for large analytical 
    jobs (also great for writing very large batches), and so which makes it OLAP ("data warehouse") instead of OLTP (which 
    is optimized for writing/updating small pieces of the db)
* https://dzone.com/articles/a-few-redshift-tips-definitions-distribution-style
  - Redshift is a data warehouse (OLAP)
  - Redshift is relational-like (e.g., b/c SQL), but is column-oriented, horizontally scalable, and elastic (typical cloud feature of sizing in-and-out, up-and-down w/o having to buy new hardware)


Wikipedia does a great job at describing a column-oriented database, how it differs from a row-oriented data base, etc:
"A column-oriented DBMS (or columnar database management system) is a database management system (DBMS) that stores 
data tables by column rather than by row. Practical use of a column store versus a row store differs little in the
relational DBMS world. Both columnar and row databases can use traditional database query languages like SQL to load 
data and perform queries. Both row and columnar databases can become the backbone in a system to serve data for common 
extract, transform, load (ETL) and data visualization tools. However, by storing data in columns rather than rows, the 
database can more precisely access the data it needs to answer a query rather than scanning and discarding unwanted data 
in rows. Query performance is increased for certain workloads."

Columnar Databses:  "In practice, columnar databases are well-suited for OLAP-like workloads (e.g., data warehouses) 
which typically involve highly complex queries over all data (possibly petabytes). However, some work must be done to 
write data into a columnar database. Transactions (INSERTs) must be separated into columns and compressed as they are 
stored, making it less suited for OLTP workloads."

Row-Oriented Databases:  "Row-oriented databases are well-suited for OLTP-like workloads which are more heavily loaded
with interactive transactions. For example, retrieving all data from a single row is more efficient when that data is 
located in a single location (minimizing disk seeks), as in row-oriented architectures."

Columnar OLAP-OLTP Hybrids: "However, column-oriented systems have been developed as hybrids capable of both OLTP 
and OLAP operations, with some of the OLTP constraints column-oriented systems face mediated using (amongst other 
qualities) in-memory data storage. Column-oriented systems suitable for both OLAP and OLTP roles effectively reduce 
the total data footprint by removing the need for separate systems."

Misc Notes
* Column-oriented database is also called a "column store"
* Regular row-oriented database may also be called a "row store" in this vernacular


From [Dzone](https://dzone.com/articles/row-store-and-column-store-databases):
"At a basic level, row stores are great for transaction processing. Column stores are great for highly 
analytical query models. Row stores have the ability to write data very quickly, whereas a column store is 
awesome at aggregating large volumes of data for a subset of columns. One of the benefits of a columnar database 
is its crazy fast query speeds. In some cases, queries that took minutes or hours are completed in seconds. This 
makes columnar databases a good choice in a query-heavy environment. But you must make sure that the queries you 
run are really suited to a columnar database."

### Further Reading for Columnar-Buzzword DBs
* Wikipedia
  - [Column Families](https://en.wikipedia.org/wiki/Column_family)
  - [Wide Column Stores](https://en.wikipedia.org/wiki/Wide_column_store)




# Graph Databases

[Dataversity](http://www.dataversity.net/a-brief-history-of-non-relational-databases/): "The strength of ACID is the guarantee it will provide a safe environment for processing data. This means data 
is consistent and stable and may use multiple memory locations. Most NoSQL Graph Databases use ACID constraints 
to ensure data is safely and consistently stored."

A graph database is for an any-to-any data model: for when you have relationshiops that go beyond tabular and 
beyond heirarchical.  From what I can tell, an interesting way to think of a graph database is as
a jerry-rigged document store:  a node can have a key and a node can have a JSON structure...so in a way, you can 
think of a graph database as a document store with connected documents.

[PWC](http://usblogs.pwc.com/emerging-technology/the-promise-of-graph-databases-in-public-health/): 
"Another way to think about structure is to remember that taxonomies, like document objects, are hierarchical 
and have just parents and children. If you need a richer classification scheme, you would use an ontology, which 
is a flexible schema, or data domain description, that articulates specific data contexts."

[PWC](http://usblogs.pwc.com/emerging-technology/the-promise-of-graph-databases-in-public-health/): 
"Instead of simply storing data as values with keys or as document objects or tables, graph stores contain nodes
and connections. Fundamentally, keys (or identifiers) and values (which could be any groupings of data) are the 
atomic building blocks for key-value, wide-column, and document stores, and these can also be the building blocks 
for graphs. Tables, documents, and graphs provide additional structure, in different shapes, with an increasing 
number of interconnections. And, as stated earlier, graphs can have many more interconnections. ... All data 
structures, from simple keys to hierarchies to graphs, can be represented to machines in the form of tags 
associated with the data."


From [Microsoft's Data Store Overview](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview):
* made of entities (nodes) and their relationships (edges)
* both nodes and edges can have attributes, similar to columns in a table (can even be similar to a JSON document)
* Relationships between objects are first-class citizens, without requiring foreign-keys and joins to traverse
* efficiently perform queries that traverse the network of nodes and edges
* efficiently analyze the relationships between entities
* use when relationships between data items are very complex, involving many hops between related data items


Notes on Amazon's Neptune graph database from AWS Database Week: NoSQL & Graph Databases (Oct 17, 2018):
* Do relationship really matter?  Would visualizing relationships quickly uncover insights
  without the need for long, tediuos, and/or complex analytical queries?
  - e.g., an example use-case for graph databases from AWS Database Week (Oct 2018) is fraud detection.  Consider
    Uber: how many times in NYC would you expect Driver X and Customer Y to interact? Once, maybe twice.  What if there 
    are 25 edges between them?  Might be fraud!
* graph DBs are for highly-connected data sets: social networks, medicine, recommendation engines, knowledge graphs, network operations
  - Knowledge graphs:  e.g., travel data
  - Life sciences:  track epidemics across globe; finding cures for new diseases (w/ protein maps)
  - Rec engine:  people who also follow sports have purchased X (triadic completion)
* Neo4j hard to scale both vertically and horizontally (?)
  - have to pay for license to scale ... (but that's similar to any AWS service :-p)
* Neptune
  - fully-managed graph db
  - horizontally scaled across many AZs
  - query w/ Gremlin and/or SPARQL
  - high performance graph engine (durable, ACID w/ immediate consistency)
  - KMS encryption at rest
  - bulk load from S3
* Neptune has Bi-directionality:  create two edges
  - this works, but can be inconvenient (e.g., any time you are FB friends w/ someone, they are FB friends w/ you)
  - Neo4j allows for a relationship to be bi-directional (maybe under the hood it's just 2 connections though)
* Neptune doesn't have a user interface ...
* Gremlin IDE
* no JDBC connector, or any database connector like that... (?)
  - there is HTTP REST endpoint
  - websocket endpoint
* Neptune is optimized for OLAP and OLTP...
* people have integrated AppSync in front of Neptune as a way to monitor things and send notifications....
* Resource:  Practical Gremlin  (free e-Book)


### Some Further Reading on GDBs
* [What are the main differences between the four types of NoSql databases, Key-Value Store, Column-Oriented Store, Document-Oriented, and Graph-Database?](https://www.quora.com/What-are-the-main-differences-between-the-four-types-of-NoSql-databases-KeyValue-Store-Column-Oriented-Store-Document-Oriented-Graph-Database)
* [Graph Modeling Do's and Don't's](http://www.durusau.net/localcopy/Graph-Modeling-Do.s-and-Don.ts.pdf)
* [Property Graphs: Swiss Army Knife Data Modeling](http://www.dataversity.net/property-graphs-swiss-army-knife-data-modeling/)
* [Relational vs Graph Data Modeling](https://dzone.com/articles/relational-vs-graph-data-modeling)


# Search Databases
These are sometimes mentioned, though not as frequently as KV, document, and column stores.

For example, they are listed as a NoSQL/nonrelations class on the [Microsft]() page.  Also, when I attended
AWS's Database Week in mid-October, 2018, one of the speakers mentioned it there too.

Notes on "Search DBs" from AWS Database Week:
* this is how a search bar is predicting your word(s) as you type
*  uses inverted index...
* e.g., Amz Elasticsearch
* seems like the "trie" data structure I learned in bioinformatics course

# Some Closing Remarks & Stuff

From [Parse.ly](https://blog.parse.ly/post/4516/cloud-sql-bigquery-redshift/): "The only reason SQL has not been the obvious first choice for analytics in the last few years is due to machine and data limitations of most common single-node SQL engines, specifically the typical Postgres and MySQL database setups you find powering the lion’s share of modern applications.  For example, if you install Postgres on a server, you will be limited by CPU, memory, disk, or all three. ... NoSQL stores solve disk limitations with horizontal data sharding strategies, such as the consistent sharding strategy famously used in Cassandra."

The above [Parse.ly](https://blog.parse.ly/post/4516/cloud-sql-bigquery-redshift/) article
discusses how "SQL of old" was on single-node hardware that you had to think a lot about: 
* how much memory should this single server have for worst case scenarios?  
* how fast do we need the CPU?  
* how much storage do we need? 
* etc, etc.  

Due to the scaling up nature of single-node "SQL of old" engines, one would need to be judicious, but not overly 
cautious:  we will definitely use more than X, but never anywhere near Z; if we go with Y, we can probably sustain growth for years to come.   

A shift from "SQL of Old" occurred when business began moving to the cloud, e.g., Amazon RDS or Google Cloud SQL.  In 
both cases, you are still very much in a "SQL of Old" territory, but you need not be as judicious and cautious when 
deciding on hardware -- start small, and scale up when the need arises.  That said, scaling up gets more and more 
expensive, and at one point you can scale no more (e.g., Amazon has caps on single-server memory, storage, etc).  Also, 
for certain weaknesses, scaling up doesn't help (e.g., database will still be slow for COUNT(DISTINCT x)).  

In comes Amazon Redshift and Google BigQuery.  

Amazon Redshift is basically as close as you can get to "SQL of Old" in terms of query language and user-end feel, 
but breaks from it in the back end, where it is column-oriented and highly optimized for OLAP needs.  You can scale 
up a single node, but also scale out to more nodes --- and so you conceptually have very few limits on things like 
memory, processing, and storage needs.  You still have to be smart about though: it can get expensive very fast, and 
Amazon will allow you to have as many nodes as you want, even if you don't need them.  BigQuery goes a step further: it 
is serverless.  That is, you just put in the data, and query it -- Google takes care of the rest.  On BQ, all the 
cluster work is done behind the scenes: if your needs require many nodes, they will be there... Also seems like 
BQ is cheaper than RS:  there is a small monthly price, a small per-TB price, and a small per-TB-queried cost.  On 
the other hand, RS starts at something like $180/month for a single-node, low-need cluster.  

Btw, now that traditionally relations DBs like PostgreSQL support many nonrelational/NoSQL features (e.g.,
KV / document store capacity), one might be tempted to think that a focused nonrelational/NoSQL solution is no 
longer necessary.  For many businesses that are not Google, Facebook, Twitter, etc, that is probably true, or 
truthy.  But if you do need to scale out, for example, a standard Postgres solution (e.g., RDS Postgres) is 
still limited...

# STILL NEED TO INTEGRATE
SOURCE:  https://www.devbridge.com/articles/benefits-of-nosql/

"It seems NoSQL is characterized more by what it is not as there is no strict technical definition of what it is and how to implement it. Still, there are some shared features present in most NoSQL database solutions: 
* Non-relational and schema-less data model 
* Low latency and high performance 
* Highly scalable"

"Arguably the biggest problem for developers using relational databases is the object-relational impedance mismatch. SQL queries are not well suited for the object oriented data structures that are used in most applications now."

"Another closely related issue is storing or retrieving an object with all relevant data. Some application operations require multiple and/or very complex queries. In that case, data mapping and query generation complexity raises too much and becomes difficult to maintain on the application side."
-- this is a document store use case:  symmetric data storage and query pattern

"Trying to cope with such a large amount of data by scaling RDBMS servers leads to configuration and maintainability issues."

KV:
"K-V store is the simplest data model. Technically it is just a distributed persistent associative array. The key is a unique identifier for a value, which can be any data application needs stored. This model is also the fastest way to get data by known key, but without the flexibility of more advanced querying."

DS:
"Document store is a data model for storing semi-structured document object data and metadata. The JSON format is normally used to represent such objects. ... Documents can be queried by their properties in a similar manner to relational databases but aren’t required to adhere to the strict structure of a database table. ... Generally speaking, document stores are used for aggregate objects that have no shared complex data between them and to quickly search or filter by some object properties."

C-O:
"A more advanced K-V store data model is a column family. These are used for organizing data based on individual columns where actual data is used as a key to refer to whole data collections. It is similar to a relational database index, however a column family may be an arbitrary collection of columns. There are more complex aggregation structures like super columns and super column families to allow access to the data by several keys. This particular approach is used for very large scalable databases to greatly reduce time for searching data. It is rarely used outside of enterprise level applications."

Graph:
"Graph databases map more directly to object oriented programming models and are faster for highly associative data sets and graph queries. Furthermore they typically support ACID transaction properties in the same way as most RDBMS."

"Many NoSQL solutions compromise consistency (in the sense of the CAP theorem) in favor of availability, scalability and partition tolerance. On the other hand, some NoSQL solutions may allow you to specify what level of consistency should be applied for particular operation and some even fully support ACID transactions. However in the case of key-value or document store data models, transaction consistency is rarely needed as most operations are by definition atomic."

-------------------------

SOURCE:  https://readwrite.com/2013/03/25/when-nosql-databases-are-good-for-you/

"NoSQL databases are designed to excel in speed and volume. To pull this off, NoSQL software will use techniques that can scare the crap out of relational database users — such as not promising that all data is consistent within a system all of the time."

"Beyond the scaling advantages, the very architecture of NoSQL tools aids performance. If a relational database had tens or even hundreds of thousands of tables, data processing would generate far more locks on that data, and greatly degrade the performance of the database. Because NoSQL databases have weaker data consistency models, they can trade off consistency for efficiency."


NOTE2SELF:  I find that when people discuss "NoSQL" databases, even though they formally include "column" and "graph," they almost exclusively write about and give examples of KV/DS in the bulk of their articles. "Column" is ambiguous b/c it refers to two separate technologies, which I think a lot of writers have not full wrapped their own heads around; also, "column-oriented" DBs/warehouses like RedShift may not be like "SQL of old" on the backend, but feel very much like "SQL of Old on the Front End," so I think many writers feel like it's less sexy to discuss in more detail.  As for "graph," it's sexy, but most writers don't seem to really understand it or use it, so just leave it as a "oh yea, there is this too."  It is known to be more relationship-oriented than relational databases and used for data that is not relational -- confuse you much?  I think that's the normal case:  "relational" data sounds like its talking about data with relationships, and graph data is highly "related" , but in truth, "relational data" here is referring to "tabular data", which is good for simple many-to-one relationships, but becomes inadequate for many-to-many, any-to-any, and recursive relationships.... Anyway, b/c of the ambiguity in terminology and general non-understanding of use cases, it is usually a side note in an article just like the "columnar" types.  POINT: "NoSQL" often is synonymous w/ "KV/DS".  


Case in Point:  "When applications developers have to work with data in relational databases, it can at times be troublesome due to data mapping and impedance issues. In NoSQL databases, this is not usually a problem, because data is not stored in the same manner. With document-oriented NoSQL databases, for instance, data is stored in just that format: documents. And since documents are objects, after all, then programmers who tend to think in object-oriented terms are going to be much more familiar with manipulating such data. That weaker consistency model helps programmers, tool, since their apps don’t have to rigidly conform to data consistency requirements. That makes coding much simpler and (by extension) much faster."
-- here, they explicitly call out "document-oriented NoSQL databases", but often I find writers do not 

Here is another "case in point" that clearly excludes Graph DBs, but does not specifically refer to a NoSQL type:  No down time is "something that non-relational databases weren’t specifically designed to do, but at which they’ve nonetheless turned out to be proficient. Because of their distributed nature ... NoSQL databases can be pretty much always on. This is a huge advantage for web- and mobile-based businesses that can’t afford to be down for a single moment. With some advanced planning, software updates and hardware upgrades can be performed while the database is still running hot. Try doing that with a relational database without taking it down, and you’re in for a world of trouble."

DO NOT SWITCH TO NoSQL IF:  "If your company has a data set that will remain relatively constant in size, or that only grows slowly, you’ll have little need to migrate to a non-relational system."

---------------------------------------------------------------

NOTE2SELF:  Basically, "NoSQL" databases arose for several reasons, and they can be categorized in various ways... Sometimes it's not very meaningful to even call a "NoSQL" database "NoSQL" -- for example, some use SQL as their query language, while others go a step further and are even relational (think Redshift).  For reasons like this, some writers talk about "relational" and "nonrelational" databases -- that is, "tabular" vs "nontabular" databases.  This distinction focuses less on query language and more on data shape.  Again, sometimes it's not a very meaningful distinction, especially as time goes on and many traditionally relational databases also offer nonrelational features (e.g., things like JSON columns in MySQL or PostgreSQL giving them some KV/DS capacity).  This brings to light other distinctions, like the ability to scale out versus up: despite having nonrelational functionality, a Postgres database might still be hard to scale out.  

---------------------------------

SOURCE:  https://blog.timescale.com/why-sql-beating-nosql-what-this-means-for-future-of-data-time-series-database-348b777b847a

"And boy did the software developer community eat up NoSQL, embracing it arguably much more broadly than the original Google/Amazon authors intended. It’s easy to understand why: NoSQL was new and shiny; it promised scale and power; it seemed like the fast path to engineering success. But then the problems started appearing."

-----------------------------------

# Resources & References
* AWS has a cool webpage called "This Is My Architecture" -- lots of videos 
* Andy Jassy's re:Invent 2017 keynote
* re:Invent 2017: Which Database to Use When? (DAT310)




