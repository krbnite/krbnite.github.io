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
## debug.log
This file is huge and diverse (lots of different info on each line), so you probably want to grep by 
date (or even up to the minute or hour):
```
cat logs/debug.log | wc -l
12050

cat logs/debug.log | grep "^2019-02-19" | wc -l
179

cat logs/debug.log | grep "^2019-02-20" | wc -l
24
```

On the days when I have very few logs, it seems most of them are stop-the-world warnings:
* e.g., `WARN [o.n.k.i.c.VmPauseMonitorComponent] Detected VM stop-the-world pause: {pauseTime=1380, gcTime=0, gcCount=0}`

These are probably days I had Neo4j Desktop still up-and-running from a previous interactive session the day before, but
never shut anything down... Just a hypothesis.

The days with many more log files have a very hetergeneous population of logs; too much info to really write
about here.

```
cat logs/debug.log  | grep "^201[89]" | awk '{ print $1 }' | uniq
2018-11-15
2018-11-16
2018-11-19
2018-11-20
2018-12-06
2018-12-11
2019-01-08
2019-01-09
2019-02-15
2019-02-18
2019-02-19
2019-02-20
2019-02-21
2019-02-22
2019-02-23
2019-02-24
```


## neo4j.log
This file is relatively small compared to the other log files I looked at.

## security.log
This file is fairly homogeneous: it mostly says when someone logged in.  In fact, for the instance I'm playing 
with right now:

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


