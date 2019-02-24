---------------------


# Loading Rows where Label is Variable

* https://stackoverflow.com/questions/24992977/neo4j-cypher-creating-nodes-and-setting-labels-with-load-csv
* https://grokbase.com/t/gg/neo4j/15bgrhqchn/setting-a-label-using-a-variable
* https://gist.github.com/jexp/caeb53acfe8a649fecade4417fb8876a


-----------------------------------------

# Exploring the Neo4j Directory Tree

First download "tree" (you won't regret it -- it's like `ls -R`, but better)
```
brew install tree
```


# Logs
## security.log
This mostly says when someone logged in.  In fact, for the instance I'm playing with right now:

```
# Number of lines about me ("neo4j") logging in
#  -- e.g., 2019-02-19 21:15:38.826+0000 INFO  [neo4j]: logged in
cat logs/security.log | grep "logged in" | wc -l
26376

# Number of lines about something else (-v = --inverted-match)
#  -- e.g., 2019-02-18 17:59:19.509+0000 INFO  [neo4j]: changed password
#  -- e.g., 2019-02-18 19:45:23.239+0000 ERROR []: failed to log in: invalid principal or credentials
cat logs/security.log | grep -v "logged in" | wc -l
14
```


