---
title: Printing Pretty Paths in Neo4j
layout: post
---

Neo4j is awesome for working with relationship-heavy data -- things that might be considered JOIN nightmares
in a relational DB.  For example, say you have designed a knowledge graph that maps lab tests to disease states, 
where a pathway might look like:

```
(:LabTest)-[:Collects]->(s1:Specimen)
    <-[:Is_Extracted_From]-(s2:Specimen)
    <-[:Is_Extracted_From]-(s3:Specimen)
    <-[:Analyzes]-(:Device)
    -[:Gives_Result]->(:Result)
    -[:Indicative_Of]->(:Disease)
```

You might have 1000s each of lab tests, specimens, devices, diseases, etc.  You might want to simply
query things like:
* Show me what lab tests are used to show evidence of diabetes
* Show me what diseases lab test A can be used to show evidence for
* Show me what lab tests can be used to collect specimen B

You can do all this very easily in Neo4j, and what's amazing is that the query will
literally look like the ASCII art above: you query as you think.  (Ironically, having said that,
the queries you will see in this post are actually pretty dang complex, but that's because the goal
is to squeeze a lot of formatting and shapeshifting out of Neo4j... You'll see.  If this is your
first time seeing the Cypher query language, turn back now!)

One of the things that Neo4j can provide is that pathway between two nodes of interest, which
is returned as a list of triples by default -- something like `[(n1,r1,n2), (n2,r2,n3), ..., (nk,rk,n[k+1])]`.

That's cool and all, but what if you want the print out to look more like the ASCII art for
a quick viz of the situation?!

After much finagling, I figured it out for the most part using the movie dataset that comes stock
with Neo4j Desktop.  This blog details my adventures.


## My First Attempt
Not very pretty, but it at least prints the label, relationship, label, 
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

## Some obvious improvements
* we don't care much about the label "Person", but the name of the person
* it would be nice to show the directionality of relationships as well

Dealing with the first issue was actually kind of easy.  Instead of getting `coalesce(labels(node),'')`,
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

## Another Few Lightbulbs
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
```

This gives:
```
direction
["-->", "<--", "-->", "<--"]
```

Now we just need to combine this logic with that we found above...

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

If returning a single string is your goal, this gets a little closer:
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

## Clean-Up Excursion
One thing is for sure: doesn't seem like you need the extract() function...ever.  The list
comprehensions seem to do the same thing.  (Please correct me if I'm wrong.)

Below, I replace any instance of `extract()` with the corresponding list comprehension.  It produces
the same output as above. 

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

## Approaching the Finish Line
I realized the answer was slightly more simple: the key is is combining the arrows and rel_types in the CASE 
statement.  Also, two of the WITH
statements can actually be cleaned up and combined into one.  

For outputting a single "ASCII Art" string:
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
```

The output does not disappoint:
```
path_string
"Keanu Reeves -[:ACTED_IN]-> The Matrix Revolutions <-[:ACTED_IN]- Hugo Weaving -[:ACTED_IN]-> Cloud Atlas <-[:ACTED_IN]- Tom Hanks "
```

NICE!!!

But, you might just want to return a list so you can format it how you want on the 
frontend, which is easy enough:
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
```

The beautiful output:
```
path_list
["Keanu Reeves", "-[:ACTED_IN]->", "The Matrix Revolutions", "<-[:ACTED_IN]-", "Hugo Weaving", "-[:ACTED_IN]->", "Cloud Atlas", "<-[:ACTED_IN]-", "Tom Hanks"]
```

El Fin... 

Or is it?

## Wrapping Up
To generalize this, there is one weak point: the coalesce function that assumes only two node types
in the traversal.  What if there are three nodes types, or more?

My hack would be to nest coalesce statements:
```
coalesce(coalesce(node.name, node.kittycat), node.title)
```

It ain't beautiful, but it will work!  
