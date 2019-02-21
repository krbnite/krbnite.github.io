
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

In a relational database, it is customary
to create a unique ID column on a table where nothing is guaranteed to be unique -- that, or the
more annoying task of creating composite IDs.  Point is, the idea of including IDs to ensure
unique rows is pretty common...  And consistent.  Normalizing a database, or deciding on a denormalized
solution, can be daunting if you require too many join tables, but it's a battle that has been
fought and won many times over.  It's routine.  The complexity is understood and documented.

In Neo4j, you can get rid of that complexity -- throw away those join tables and extraneous IDs!  You
replace these things with easier-to-understand relationships.  However, complexity does seep in.  For example,
it can feel like you're making things up as you go along -- maybe even reinventing the wheel.  Maybe you're
just doing things wrong.  Maybe not.  It's hard to know...

What's my point?  I guess I'm getting to it:  there is some complexity hidden in MERGE.  It has to do
with constaints, IDs, creation, etc.  

For the moment, I think it makes sense to think of the graph database like a relational database:
consider nodes of a certain label L1 to be rows in a table called "L1" and other nodes of label L2
to be rows in a table called "L2".  Say L1 is :Author and L2 is :Book.  Assume an author's name is 
unique, but a book's title is not -- but that a book's title and the book's author do make a unique 
combo.  Then the author's name would be a primary key in the "Author" table and a foreign key
in the "Book" table, which would have composite primary key (author's name and book title).  Point is,
placing a uniqueness constraint on both tables is easy, straigtforward, and routine.

What about the Neo4j version of this?  Well you can create a uniqueness constraint on the author's name. But
what about the :Book label nodes?  If you want to create a uniqueness constraint, then you must include
the author's name -- correctly.  The database won't exactly use a foreign key to ensure this... You have to ensure
it yourself.  The other possibility, also at your own application layer, is using MERGE...  This will ensure
that a book title is always associated with an author, and unique for the author.  However, this still isn't the same:
if you issued a CREATE statement in the relational case, it would get rejected: MERGE will MATCH or CREATE, and 
on MATCH will update the data as you specify.  It won't get rejected...


-----------------------------

I guess it's true for any language or technology, but you really have to know what you're doing.  

Another example:  say you want to MATCH something, then update it.  If you use MERGE and it doesn't exist,
it will be created and updated.  If you use Merge and it does exist, it will just be updated.  If use MATCH
and its exists, again it will be updated.  But if you use MATCH and it doesn't exist, nothing will be
updated -- but you also won't be notified.  There is no error or hiccup of any kind.  

