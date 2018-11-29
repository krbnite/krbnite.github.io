# Notes from Oct 15, 2018

SOURCE:  https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview

The heterogeneity of database use cases these days "means that a single data store is usually not the best approach. Instead, it's often better to store different types of data in different data stores, each focused towards a specific workload or usage pattern. The term polyglot persistence is used to describe solutions that use a mix of data store technologies."

> "...a particular data store technology may support multiple storage models. For example, a relational database management systems (RDBMS) may also support key/value or graph storage. In fact, there is a general trend for so-called multimodel support, where a single database system supports several models."

### Relational DB
* Relational (tabular) databases organize data as a series of two-dimensional tables with rows and columns. 
* The query language is almost always a dialect of SQL
* Many RDBMSs offer ACID-compliant transactions for updating information
* Schema is specified ahead of time, and all read/write operations respect the schema
* Strong consistency guarantees
* Tabularized information is put into relational structure by the normalization process, which can be complex and unintuitive when querying

### KV Store
* A key/value store is basically a large hash table (hashing function helps distribute data evenly across system)
* Most only support simple query, insert, and delete operations
* The stored values are opaque to the storage system software (values are essentially blobs)
* Any schema information must be provided and interpreted by the application
* highly optimized for applications performing simple lookups
* not suitable for systems that need to query data across different key/value stores
* not optimized for scenarios where querying by value is important (i.e., no WHERE clause; lookups are based only on keys!)
* extremely scalable due to its complete lack of relationships between KV pairs (can easily distribute data across multiple nodes on separate machines)
* not typically used for data that requires updates, which are basically all-or-none (full rewrites)

### Document DBs
* conceptually similar to a key/value store, except that its values are documents, not "blobs"
* a "document" may be encoded in XML, YAML, JSON, BSON, or even stored as plain text
* similar to a KVS, these documents can be accessed by key (often hashed to help distribute data evenly) 
* however, documents are not opaque to the storage management system (unlike the "blobs" of KV stores), so one is also able query and filter data by using the values in a document's fields
* documents are usually entity-oriented, i.e., a single document contains the entire data for an entity (unlike in a relational DB, where this info would be distributed across many tables)
* documents of the same type need not all have the same structure (flexible schema)
* some document stores support indexing to facilitate fast lookups based on one or more fields
* can update fields in a document w/o rewriting entire document (unlike a KVS blob, which is basically an all-or-none update)

### Column-Family DBs
* organizes data into rows and columns (has some appearances of a traditional relational database)
* implements a powerful, denormalized approach to structuring sparse data 
* columns are split into logically-related groups called "column families" for columns that are typically retrieved or manipulated as a unit
* ....

### Graph DBs
* made of entities (nodes) and their relationships (edges)
* both nodes and edges can have attributes, similar to columns in a table (can even be similar to a JSON document)
* Relationships between objects are first-class citizens, without requiring foreign-keys and joins to traverse
* efficiently perform queries that traverse the network of nodes and edges
* efficiently analyze the relationships between entities
* use when relationships between data items are very complex, involving many hops between related data items

Amazing bullet-point breakdown of various DB types:
* https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-comparison

It's basically worth copy-and-pasting that.....


--------------------------------------------------------------------

SOURCE: https://www.pluralsight.com/blog/software-development/relational-non-relational-databases

I could be wrong, but document stores seem to have basically come about due to the needs and gripes of JavaScript-heavy app developers.  A front-end developer uses JSON objects to push around data for this and that, and the idea was "Why not also push JSON to the back-end as well?"  The other idea was, "Why SQL when we can just keep using JavaScript?"  Thus the MEAN stack.  This way you push and pull the data as JSON objects, but lose out on joins... But if you don't need joins, then who cares?  Not the devs who pushed for document stores: you can technically join data in your app code.  You might miss out on optimization, constraints, and integrity this way -- up to you and your own talents, I suppose.  One of the complaints is object-relational impedance mismatching, which I understand to have to do w/ the complexity in object-relational mapping, and the associated latency.  Anyway, point is, they be like: "Why no just keep in JS?!?!"

> "The structure of a relational database allows you to link information from different tables through the use of foreign keys (or indexes), which are used to uniquely identify any atomic piece of data within that table. Other tables may refer to that foreign key, so as to create a link between their data pieces and the piece pointed to by the foreign key. This comes in handy for applications that are heavy into data analysis."

> "If you want your application to handle a lot of complicated querying, database transactions and routine analysis of data, you’ll probably want to stick with a relational database. And if your application is going to focus on doing many database transactions, it’s important that those transactions are processed reliably. This is where ACID (the set of properties that guarantee database transactions are processed reliably) really matters, and where referential integrity comes into play."

> "A non-relational database just stores data without explicit and structured mechanisms to link data from different tables (or buckets) to one another."

"""
Other reasons for choosing a non-relational database include: 
* The need to store serialized arrays in JSON objects 
* Storing records in the same collection that have different fields or attributes 
* Finding yourself de-normalizing your database schema or coding around performance and horizontal scalability issues 
* Problems easily pre-defining your schema because of the nature of your data model
"""

At EaSi, we don't need any of this...

> "In non-relational databases like Mongo, there are no joins like there would be in relational databases. This means you need to perform multiple queries and join the data manually within your code -- and that can get very ugly, very fast."

--------------------------------------------------------------------

SOURCE: https://www.jamesserra.com/archive/2015/08/relational-databases-vs-non-relational-databases/

This guy does a great job of summarizing KV and DocStore database motivations:

> "The bottom line for using a NoSQL solution is if you have an OLTP application that has thousands of users and has a very large database requiring a scale-out solution and/or is using JSON data, in particular if this JSON data has various structures. You also get the benefit of high availability as NoSQL solutions store multiple copies of the data. Just keep in mind you for performance you may sacrifice data consistency, as well as the ability to join data, use SQL, and to do quick mass updates."

Remember, many NoSQL solutions began cropping up and "became popular with the introduction of the web, when databases went from a max of a few hundred users on an internal company application to thousands or millions of users on a web application."

-------------------------------------------------------------------------

SOURCE:  http://www.dataversity.net/a-brief-history-of-non-relational-databases/

> "In the mid-1990s, the internet gained extreme popularity, and relational databases simply could not keep up with the flow of information demanded by users, as well as the larger variety of data types that occurred from this evolution. This led to the development of non-relational databases, often referred to as NoSQL. NoSQL databases can translate strange data quickly and avoid the rigidity of SQL by replacing “organized” storage with more flexibility."

> "NoSQL developed at least in the beginning as a response to web data, the need for processing unstructured data, and the need for faster processing. The NoSQL model uses a distributed database system, meaning a system with multiple computers."

> "The strength of ACID is the guarantee it will provide a safe environment for processing data. This means data is consistent and stable and may use multiple memory locations. Most NoSQL Graph Databases use ACID constraints to ensure data is safely and consistently stored."

On BASE:  
> "Availability for scaling purposes is an important feature for BASE data stores. However, it doesn’t offer the guarantee of consistency for replicated data during write time. The BASE model, generally speaking, provides less assurance than ACID. BASE is used primarily by aggregate stores, which includes column, document, and key value stores."

> "While the non-relational database has certain strengths when storing data, it also comes with a significant drawback – key-value stores cannot “enforce” the relationships between items. ...the relational model comes with a built-in, foolproof way of ensuring business logic and trustworthiness at the database layer. ... This is why relational databases continue to be popular."

> "However, the highest priority for web-based applications is the ability to service large numbers of user requests, which is the strength of non-relational databases."  e.g., the eBay webpage is more concerned w/ "quick response time and wants to assure fast page loading, rather than enforcing strict business rules."

-----------------------------------------------------------------------------------------------------

## GRAPHS

SOURCE:  http://usblogs.pwc.com/emerging-technology/the-promise-of-graph-databases-in-public-health/

* any-to-any data model: not just tabular; not just heirarchical
* interesting: a node can have a key and a node can have a JSON structure...so in a way, you can think of a graph db as a document store w/ connected documents... writing that out felt like "duh", but it's a great insight...


"Another way to think about structure is to remember that taxonomies, like document objects, are hierarchical and have just parents and children. If you need a richer classification scheme, you would use an ontology, which is a flexible schema, or data domain description, that articulates specific data contexts."

Kind of what struck me above:
"Instead of simply storing data as values with keys or as document objects or tables, graph stores contain nodes and connections. Fundamentally, keys (or identifiers) and values (which could be any groupings of data) are the atomic building blocks for key-value, wide-column, and document stores, and these can also be the building blocks for graphs. Tables, documents, and graphs provide additional structure, in different shapes, with an increasing number of interconnections. And, as stated earlier, graphs can have many more interconnections. ... All data structures, from simple keys to hierarchies to graphs, can be represented to machines in the form of tags associated with the data."

--------------------------------

https://www.quora.com/What-are-the-main-differences-between-the-four-types-of-NoSql-databases-KeyValue-Store-Column-Oriented-Store-Document-Oriented-Graph-Database

http://www.durusau.net/localcopy/Graph-Modeling-Do.s-and-Don.ts.pdf

http://www.dataversity.net/property-graphs-swiss-army-knife-data-modeling/

https://dzone.com/articles/relational-vs-graph-data-modeling

----------------

Interesting article on the difference between a column-oriented database (very similar to traditional, but col-oriented) and a general "column store" -- basically, both use the idea of "column family".... Example he gives: BigTable, HBase, and Cassandra are often called "column-oriented databases" just b/c they do "column families" ... He (and others) point out that it is very misleading to refer to these as "column-oriented".  
http://bytecontinnum.com/2016/07/column-stores-column-oriented-databases/

This article was a refreshing b/c only moments before, I was having this strange feeling that "column stores" seem to be inconsistently described... Pointing out that almost everyone incorrectly conflates a bunch of things into "column oriented" was helpful....  There are other articles on this:
* https://stackoverflow.com/questions/25441921/is-cassandra-a-column-oriented-or-columnar-database
* https://stackoverflow.com/questions/13010225/why-many-refer-to-cassandra-as-a-column-oriented-database
* http://dbmsmusings.blogspot.com/2010/03/distinguishing-two-major-types-of_29.html


###################################################################


# Notes from Oct 17, 2018

AWS Database Week: NoSQL & Graph Databases

Non-relational Revolution
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
-- e.g., what if each record does not care about any other record?  (no need to scan a whole table, maybe use KV)
-- e.g., what if each instance of a full entity doesn't care about other entities?  (no need to scan and join tables, maybe use a docStore)
* Graph DB
- Fraud detection: think about Uber: how many times in NYC would you expect Driver X and Customer Y to interact?  Once, maybe twice.  What if there are 25 edges between them?  Might be fraud!
* Search DB
- Note: second time I've seen "Search DBs" listed as a NoSQL class (first time on MS webpage)
- this is how a search bar is predicting your word(s) as you type
- uses inverted index...
- e.g., Amz Elasticsearch
- seems like the "trie" data structure I learned in bioinformatics course
* In-Memory
- I see this listed as a NoSQL class every so often, but to me it seems like a DB feature more than a DB class (e.g., Redis is an in-memory DB, but isn't it also a KV DB?)
* Also went over Relational, KV, Document
- interestingly, Redshift is listed as relational ... other times, I've seen it listed as column-oriented
* this really gets into how you categorize:  by feel (obviously Redshift "feels" relational), by storage model, by query language, etc
- even AWS sells Redshift as "relational", so it's interesting: we've come to a point in time where we are no longer strictly delineating databases by how they work under the hood
* Resource
- AWS has a cool webpage called "This Is My Architecture" -- lots of videos 
- Andy Jassy's re:Invent 2017 keynote
- re:Invent 2017: Which Database to Use When? (DAT310)

Amazon ElastiCache
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



Graph and Amazon Neptune
* graph DBs are for highly-connected data sets: social networks, medicine, recommendation engines, knowledge graphs, network operations
* Knowledge graphs:  e.g., travel data
* Life sciences:  track epidemics across globe; finding cures for new diseases (w/ protein maps)
* Rec engine:  people who also follow sports have purchased X (triadic completion)
* Neo4j hard to scale both vertically and horizontally (?)
-- have to pay for license to scale ... (but that's similar to any AWS service :-p)
* Neptune
- fully-managed graph db
- horizontally scaled across many AZs
- query w/ Gremlin and/or SPARQL
- high performance graph engine (durable, ACID w/ immediate consistency)
- KMS encryption at rest
- bulk load from S3
* Bi-directionality:  create two edges
- this works, but can be inconvenient (e.g., any time you are FB friends w/ someone, they are FB friends w/ you)
- Neo4j allows for a relationship to be bi-directional (maybe under the hood it's just 2 connections though)
* graph exp
* Neptune doesn't have UI ...
* Gremlin IDE
* no JDBC connector, or any database connector like that... (?)
- there is HTTP REST endpoint
- websocket endpoint
* Neptune is optimized for OLAP and OLTP...
* people have integrated AppSync in front of Neptune as a way to monitor things and send notifications....
* Resource:  Practical Gremlin  (free e-Book)



----------------------------------------------------------------------

### What is Redshift?
* https://docs.aws.amazon.com/redshift/latest/dg/c_columnar_storage_disk_mem_mgmnt.html
* It is considered a RDBMS, but is a column-oriented database, which make it read-optimized for large analytical jobs (also great for writing very large batches), and so which makes it OLAP ("data warehouse") instead of OLTP (which is optimized for writing/updating small pieces of the db)

* https://dzone.com/articles/a-few-redshift-tips-definitions-distribution-style
* it is a data warehouse (OLAP)
* it is relational-like (e.g., b/c SQL), but is column-oriented, horizontally scalable, and elastic (typical cloud feature of sizing in-and-out, up-and-down w/o having to buy new hardware)

-----------------------------------

### Think of needs:
* does you DB just need to find info on a document to quickly write to or read from, where the document might not easily relate to other documents, but also -- the use case doesn't call for it
* or, maybe your DB needs to relate data across many different entities and business verticals, very quickly:  how quick do you need joining, searching, and pattern matching to be?
* does it need to retrieve and sort (order by) quickly

Btw, in bizspeak: 
- "reporting" simply refers to "how much/many?" (e.g., select count(customers) from cust_tbl)
- "analysis" refers to the "why" of the reporting: why so few this week? why the spike? (e.g., select count(website_hits) from hits_tbl group by traffic_source, device_type, etc)

-----------------------------------------------------------------------

SOURCE:  https://blog.parse.ly/post/4516/cloud-sql-bigquery-redshift/
** AMAZING ARTICLE
> 
"The only reason SQL has not been the obvious first choice for analytics in the last few years is due to machine and data limitations of most common single-node SQL engines, specifically the typical Postgres and MySQL database setups you find powering the lion’s share of modern applications.  For example, if you install Postgres on a server, you will be limited by CPU, memory, disk, or all three. ... NoSQL stores solve disk limitations with horizontal data sharding strategies, such as the consistent sharding strategy famously used in Cassandra."

Discusses how "SQL of old" was on single-node hardware that you had to think a lot about: how much memory should this single server have for worst case scenarios?  how fast do we need the CPU?  how much storage do we need? etc, etc.  Due to the scaling up nature of single-node "SQL of old" engines, one would need to be judicious, but not overly cautious:  we will definitely use more than X, but never anywhere near Z; if we go with Y, we can probably sustain growth for years to come.   However, the first shift from "SQL of Old" is moving to the cloud, e.g., Amazon RDS or Google Cloud SQL.  In both cases, you are still very much in a "SQL of Old" territory, but you need not be as judicious and cautious when deciding on hardware -- start small, and scale up when the need arises.  That said, scaling up gets more and more expensive, and at one point you can scale no more (e.g., Amazon has caps on single-server memory, storage, etc); also, for certain weaknesses, scaling up doesn't help (e.g., database will still be slow for COUNT(DISTINCT x)).  In comes Amazon Redshift and Google BigQuery.  Amazon Redshift is basically as close as you can get to "SQL of Old" in terms of query language and user-end feel, but breaks from it in the back end, where it is column-oriented and highly optimized for OLAP needs.  You can scale up a single node, but also scale out to more nodes --- and so you conceptually have very few limits on things like memory, processing, and storage needs.  You still have to be smart about though: it can get expensive very fast, and Amazon will allow you to have as many nodes as you want, even if you don't need them.  BigQuery goes a step further: it is serverless.  That is, you just put in the data, and query it -- Google takes care of the rest.  On BQ, all the cluster work is done behind the scenes: if your needs require many nodes, they will be there... Also seems like BQ is cheaper than RS:  there is a small monthly price, a small per-TB price, and a small per-TB-queried cost.  On the other hand, RS starts at something like $180/month for a single-node, low-need cluster.  

Btw, now that traditionally relations DBs like PostgreSQL support many nonrelational/NoSQL features (e.g., KV / document store capacity), one might be tempted to think that a focused nonrelational/NoSQL solution is no longer necessary.  For many businesses that are not Google, Facebook, Twitter, etc, that is probably true, or truthy.  But if you do need to scale out, for example, a standard Postgres solution (e.g., RDS Postgres) is still limited...

Highly recommend reading again...

-----------------------------------------------------------------------

SOURCE:  https://www.devbridge.com/articles/benefits-of-nosql/

> "It seems NoSQL is characterized more by what it is not as there is no strict technical definition of what it is and how to implement it. Still, there are some shared features present in most NoSQL database solutions: 
> * Non-relational and schema-less data model 
> * Low latency and high performance 
> * Highly scalable"

> "Arguably the biggest problem for developers using relational databases is the object-relational impedance mismatch. SQL queries are not well suited for the object oriented data structures that are used in most applications now."

> "Another closely related issue is storing or retrieving an object with all relevant data. Some application operations require multiple and/or very complex queries. In that case, data mapping and query generation complexity raises too much and becomes difficult to maintain on the application side."
-- this is a document store use case:  symmetric data storage and query pattern

> "Trying to cope with such a large amount of data by scaling RDBMS servers leads to configuration and maintainability issues."



### KV
> "K-V store is the simplest data model. Technically it is just a distributed persistent associative array. The key is a unique identifier for a value, which can be any data application needs stored. This model is also the fastest way to get data by known key, but without the flexibility of more advanced querying."

### DS:
> "Document store is a data model for storing semi-structured document object data and metadata. The JSON format is normally used to represent such objects. ... Documents can be queried by their properties in a similar manner to relational databases but aren’t required to adhere to the strict structure of a database table. ... Generally speaking, document stores are used for aggregate objects that have no shared complex data between them and to quickly search or filter by some object properties."

### C-O:
> "A more advanced K-V store data model is a column family. These are used for organizing data based on individual columns where actual data is used as a key to refer to whole data collections. It is similar to a relational database index, however a column family may be an arbitrary collection of columns. There are more complex aggregation structures like super columns and super column families to allow access to the data by several keys. This particular approach is used for very large scalable databases to greatly reduce time for searching data. It is rarely used outside of enterprise level applications."

### Graph:
> "Graph databases map more directly to object oriented programming models and are faster for highly associative data sets and graph queries. Furthermore they typically support ACID transaction properties in the same way as most RDBMS."



> "Many NoSQL solutions compromise consistency (in the sense of the CAP theorem) in favor of availability, scalability and partition tolerance. On the other hand, some NoSQL solutions may allow you to specify what level of consistency should be applied for particular operation and some even fully support ACID transactions. However in the case of key-value or document store data models, transaction consistency is rarely needed as most operations are by definition atomic."

-----------------------------

SOURCE: https://veesp.com/en/blog/sql-or-nosql

> "[R]elational DBs occupy the top position on the market giving odds to NoSQL solutions. This may be explained by the fact that NoSQL aren’t able to perform SQL tasks better than SQL itself. The best NoSQL solutions cope with specific tasks and are usually created by leading IT companies, such as Google, Amazon, Microsoft and Apache to deal with their needs."

> "Overall, there are four main types of NoSQL stores. They differ in data model, distributivity and replication approaches, as a result they can successfully tackle different kinds of tasks."

...GO BACK TO THIS...


--------------------------------------------------------------------------

SOURCE:  https://readwrite.com/2013/03/25/when-nosql-databases-are-good-for-you/

> "NoSQL databases are designed to excel in speed and volume. To pull this off, NoSQL software will use techniques that can scare the crap out of relational database users — such as not promising that all data is consistent within a system all of the time."

> "Beyond the scaling advantages, the very architecture of NoSQL tools aids performance. If a relational database had tens or even hundreds of thousands of tables, data processing would generate far more locks on that data, and greatly degrade the performance of the database. Because NoSQL databases have weaker data consistency models, they can trade off consistency for efficiency."


**NOTE2SELF**:  I find that when people discuss "NoSQL" databases, even though they formally include "column" and "graph," they almost exclusively write about and give examples of KV/DS in the bulk of their articles. "Column" is ambiguous b/c it refers to two separate technologies, which I think a lot of writers have not full wrapped their own heads around; also, "column-oriented" DBs/warehouses like RedShift may not be like "SQL of old" on the backend, but feel very much like "SQL of Old on the Front End," so I think many writers feel like it's less sexy to discuss in more detail.  As for "graph," it's sexy, but most writers don't seem to really understand it or use it, so just leave it as a "oh yea, there is this too."  It is known to be more relationship-oriented than relational databases and used for data that is not relational -- confuse you much?  I think that's the normal case:  "relational" data sounds like its talking about data with relationships, and graph data is highly "related" , but in truth, "relational data" here is referring to "tabular data", which is good for simple many-to-one relationships, but becomes inadequate for many-to-many, any-to-any, and recursive relationships.... Anyway, b/c of the ambiguity in terminology and general non-understanding of use cases, it is usually a side note in an article just like the "columnar" types.  POINT: "NoSQL" often is synonymous w/ "KV/DS".  


**Case in Point**:  
> "When applications developers have to work with data in relational databases, it can at times be troublesome due to data mapping and impedance issues. In NoSQL databases, this is not usually a problem, because data is not stored in the same manner. With document-oriented NoSQL databases, for instance, data is stored in just that format: documents. And since documents are objects, after all, then programmers who tend to think in object-oriented terms are going to be much more familiar with manipulating such data. That weaker consistency model helps programmers, tool, since their apps don’t have to rigidly conform to data consistency requirements. That makes coding much simpler and (by extension) much faster."
-- here, they explicitly call out "document-oriented NoSQL databases", but often I find writers do not 

Here is another "case in point" that clearly excludes Graph DBs, but does not specifically refer to a NoSQL type:  No down time is "something that non-relational databases weren’t specifically designed to do, but at which they’ve nonetheless turned out to be proficient. Because of their distributed nature ... NoSQL databases can be pretty much always on. This is a huge advantage for web- and mobile-based businesses that can’t afford to be down for a single moment. With some advanced planning, software updates and hardware upgrades can be performed while the database is still running hot. Try doing that with a relational database without taking it down, and you’re in for a world of trouble."

DO NOT SWITCH TO NoSQL IF:  "If your company has a data set that will remain relatively constant in size, or that only grows slowly, you’ll have little need to migrate to a non-relational system."

---------------------------------------------------------------

**NOTE2SELF**:  Basically, "NoSQL" databases arose for several reasons, and they can be categorized in various ways... Sometimes it's not very meaningful to even call a "NoSQL" database "NoSQL" -- for example, some use SQL as their query language, while others go a step further and are even relational (think Redshift).  For reasons like this, some writers talk about "relational" and "nonrelational" databases -- that is, "tabular" vs "nontabular" databases.  This distinction focuses less on query language and more on data shape.  Again, sometimes it's not a very meaningful distinction, especially as time goes on and many traditionally relational databases also offer nonrelational features (e.g., things like JSON columns in MySQL or PostgreSQL giving them some KV/DS capacity).  This brings to light other distinctions, like the ability to scale out versus up: despite having nonrelational functionality, a Postgres database might still be hard to scale out.  

-----------------------------------------------------------------------

SOURCE:  https://blog.timescale.com/why-sql-beating-nosql-what-this-means-for-future-of-data-time-series-database-348b777b847a

"And boy did the software developer community eat up NoSQL, embracing it arguably much more broadly than the original Google/Amazon authors intended. It’s easy to understand why: NoSQL was new and shiny; it promised scale and power; it seemed like the fast path to engineering success. But then the problems started appearing."







#####################################################################

------------------------------------------------


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


### Need Documents
What's cool about Neo4j is that you can output the data almost any way you want: you can return 
nodes (which are basically shallow JSON documents), tables if you want, or full-on JSON document
structures.  

For example, take the movie data set.  Using projection and aggregation, you can return "actor documents",
which have an actor at the top level of heirarchy, and a movies key, which stores all movie info and what
roles they played.  

```
match (p:Person)-[r:ACTED_IN]->(m:Movie)
return p { .*,  movies: collect(m { .*, roles: r.roles})}
```

Better, since this is a graph database, you automatically can invert the relationship and 
return "movie documents" that list all actors and their roles in that movie.  

```
match (p:Person)-[r:ACTED_IN]->(m:Movie)
return m { .*,  actors: collect(p { .*, roles: r.roles})}
```

The query is almost no different, except in how you aggregate and format the output.

Very cool!

################################################
