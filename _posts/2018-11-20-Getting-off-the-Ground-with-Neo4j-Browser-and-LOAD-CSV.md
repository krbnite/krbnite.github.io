---
title: Getting off the Ground with Neo4j Browser and LOAD CSV
layout: post
tags: databases neo4j easi
---

Have I ever mentioned that I've given up trying to be creative with post names?  

Yes, probably I did.  Moving on!

In this post, I passionately deliver an expose' on the most basic of LOAD CSV usage.  
Without skipping a beat, I feverishly document how I got started with Neo4j Browser after having only 
tinkered with Neo4j Desktop (SPOILER ALERT: NBD).  At times you will laugh, and at times you'll
cry -- that's just general life stuff though, not really anything to do with this post per se.  

# LOAD CSV
Basically, on the project I'm currently working on, we need to start thinking about how we're going to
load all the data...which is scattered about hard drives in various patterns of organization and anarchy, 
and in myriad states of completion.  We've defined our graph schema: our entities (nodes) and all kinds of 
relationships between these things -- recursive, heirarchical, self-referencing. The graph data model 
we developed is for highly relationship-oriented data and queries of interest -- things that would
require absoltuely mind-numbing JOINs in a relational database.  Not impossible... But being struck
by lightning twice isn't impossible either... The graph data model really seemed to help here.  

Having said that, we're also in the position of organizing the data into tabular CSV files just to load into
the graph.  So it's kind of like developing for a highly-denormalized relational database anyway.  Furthermore,
once we decide on how to best organize all our data into these CSV files, using LOAD CSV correctly is just
the start: we also have to use the CREATE and MERGE statement in a way that actually loads the data
in the way we think it's loading the data!  (That is, with no unanticipated side effects, e.g., creating the
same node multiple times.)

So... Anyway!  Enough background.  Fact is, first time I tried to load a CSV file using `LOAD CSV` I got 
an error, and that error led to this post.

## Where should the CSV file live?
First thing you'll find out is you can't just load a CSV file from anywhere
(without messing with the configuration file like done in this 
[YouTube video](https://www.youtube.com/watch?v=JhZaCw94r40)).  That video shows you that there is a
configuration file that you can modify to allow CSV files to be loaded from anywhere, but be warned: it
is not a recommended practice for anything in production.

By default, you can only load CSV file from a database's `import` directory.  For example,
one of the databases on my computer is located at:
```
/Users/Me/Library/Application Support/Neo4j Desktop/Application/neo4jDatabases/database-l0ng-h3xaD3cIm2l-c0d3/installation-3.4.7/import
```

## Basic Usage
Imagine the following CSV file, movies1.csv:
```
actor,movie,roles
Joe Schmoe,Think a Thought,Knight
Joe Schmoe,Think a Thought,King
Joe Schmoe,Think a Thought,Ghostly Figure
Jimmy John,Tinker Pop,Jedi Ninja
Jimmy John,Tinker Pop,Surfer Dude
Kelly Smelly,Life's Like That,Kim Schmim
```

You can then load that file into Neo4j:
```
LOAD CSV WITH HEADERS FROM 'file:///movies1.csv' AS line
MERGE (p:Person { name: line.actor })
  -[r:ACTED_IN]->(m:Movie { title: line.movie })
  ON CREATE SET r.roles = [line.roles]
  ON MATCH SET r.roles = r.roles + [line.roles]
```

There are some caveats on this query. Keep reading!

## Better Usage
The query above

Imagine the following CSV file, movies1.csv:
```
actor,movie,roles
Joe Schmoe,Think a Thought,Knight
Joe Schmoe,Think a Thought,King
Joe Schmoe,Think a Thought,Ghostly Figure
Jimmy John,Tinker Pop,Jedi Ninja
Jimmy John,Tinker Pop,Surfer Dude
Kelly Smelly,Life's Like That,Kim Schmim
Kelly Smelly,Life's Like That Too,Kim Schmim
Kelly Smelly,Life's Like That Too,Pam Schmam
```

If you use the above query on this CSV file, it will not do what you think it might be doing... That is,
it will not be creating just one `Kelly Smelly` node.  Instead, it creates two like so:

```
MATCH (p:Person {name:"Kelly Smelly"}) -[r]-> (m:Movie)
RETURN id(p) AS ID, p.name AS Actor, m.title AS Movie, r.roles AS Roles

  ID	Actor	Movie	Roles
  57	"Kelly Smelly"	"Life's Like That"	["Kim Schmim"]
  59	"Kelly Smelly"	"Life's Like That Too"	["Kim Schmim", "Pam Schmam"]
```

That's definitely not what we wanted!  There isn't just one `Kelly Smelly` node: there are two!  Node IDs 57
and 59.  But why? The problem is in the single `MERGE` statement.  Remember,
`MERGE` basically means `MATCH OR CREATE`, so if some parts of the graph pattern exist, but not others
you can get a funky result like this.

Ok, let's try this again.  First, delete the current data:
```
MATCH (n) DETACH DELETE n
```

Then `MERGE` on each of the items separately:  
```
LOAD CSV WITH HEADERS FROM 'file:///movies1.csv' AS line
MERGE (p:Person { name: line.actor })
MERGE (m:Movie { title: line.movie })
MERGE (p)-[r:ACTED_IN]->(m) 
  ON CREATE SET r.roles = [line.roles]
  ON MATCH SET r.roles = r.roles + [line.roles]
```

Test it again:
```
MATCH (p:Person {name:"Kelly Smelly"}) -[r]-> (m:Movie)
RETURN id(p) AS ID, p.name AS Actor, m.title AS Movie, r.roles AS Roles

  ID	Actor	Movie	Roles
  52	"Kelly Smelly"	"Life's Like That"	["Kim Schmim"]
  52	"Kelly Smelly"	"Life's Like That Too"	["Kim Schmim", "Pam Schmam"]
```

Yay -- just one `Kelly Smelly` node!  

## Making a CSV file's "shape" a bit more like your graph
While reading about indexing in the Cypher Manual, I saw that we can structure CSV files in a potentially
better way for ingestion:  basically, you can include arrays in a CSV file by using non-comma separator, like ‘;’,
then use the `split` function when loading it into Neo4j.

For example, imagine the following CSV file, movies2.csv:
```
actor,movie,roles
Joe Schmoe,Think a Thought,Knight;King;Ghostly Figure
Jimmy John,Tinker Pop,Jedi Ninja;Surfer Dude
Kelly Smelly,Life's Like That,Kim Schmim
Kelly Smelly,Life's Like That Too, KimSchim;Pam Schmam
```

This is slighly de-normalized now, but not fully: we still need to mind our `MERGE`s.  
```
LOAD CSV WITH HEADERS FROM 'file:///movies2.csv' AS line
MERGE (p:Person { name: line.actor })
MERGE (m:Movie {title: line.movie})
MERGE (p) -[:ACTED_IN { roles: split(line.roles, ';')}]-> (m)
```

The use and misuse of `MERGE` is nicely covered in 
[Common Confusion on Cypher (and How to Avoid Them)](https://neo4j.com/blog/common-confusions-cypher/).


# Neo4j Browser
When I was messing around with figuring out where to store CSV files so I could load them into Neo4j, 
I learned how to start up a Neo4j database from the command line (something I've been meaning to get 
around to).

Basically, it is (almost) as simple as:
```
## ASSUMING YOU ARE IN YOUR GRAPH DB'S TOP DIRECTORY

# Start it up
./bin/neo4j start
open http://localhost:7474

# Shut it down
./bin/neo4j stop
```

But not so fast!  You'll likely get an error saying something about being "unable to find any JVMs"
matching the required version.  A quick Google 1-2 finds that this is because we need to first make sure
the JAVA_HOME environment variable is defined ([source on StackOverflow](https://stackoverflow.com/questions/29731193/unable-to-find-any-jvms-matching-version-1-7-while-starting-neo4j)).

```
# Start it up
export JAVA_HOME=$(/usr/libexec/java_home)
source ~/.bash_profile
./bin/neo4j start
open http://localhost:7474
```

You'll have to remember your password to sign in -- D'oh!

-------------------------------------------

## Some References
* Neo4j Guide: [Importing CSV Data into Neo4j](https://neo4j.com/developer/guide-import-csv/)
* Cypher Manual: [LOAD CSV](https://neo4j.com/docs/cypher-manual/current/clauses/load-csv/)
* StackOverflow: [Unable to find any JVMs matching version 1.7 while starting Neo4j](https://stackoverflow.com/questions/29731193/unable-to-find-any-jvms-matching-version-1-7-while-starting-neo4j)
* YouTube: [Neo4j Tutorial: Upload CSV File into Neo4j](https://www.youtube.com/watch?v=JhZaCw94r40) 
* Neo4j Developer: [Understanding How Merge Works](https://neo4j.com/developer/kb/understanding-how-merge-works/)
* Neo4j Blog: [Common Confusion on Cypher (and How to Avoid Them)](https://neo4j.com/blog/common-confusions-cypher/)
