---
layout: post
title:  Connect to Redshift from R with RPosgreSQL
tags: databases redshift r
---


At work, a lot of people use DBVisualizer on Windows computers... This was ok to get up and running, e.g.,
learning about the various schemas and tables we have in Redshift.  But at one point, its utility is lacking:
you can't really do anything with the data without writing it to a CSV file and picking it up in R or Python. So why
not cut out the middle man and just query data from R or Python?  

Here, I provide some notes I took while figuring it out myself.

You will need Java, e.g., on Linux Ubuntu you should do something like this:
```
sudo apt-get install openjdk-9-jre
sudo apt-get install libpq-dev
```

Then, install the rJava and RPostgreSQL packages in R:
```r
install.packages(c('rJava', 'RPostgreSQL'))
```

Finally, to access:
```r
library(RPostgreSQL)
drv=dbDriver("PostgreSQL")
host='yourRedShiftInstance.redshift.amazonaws.com'
con = dbConnect(drv, host=host_production, 
    port='5439',
    dbname='dbName', 
    user='yourUserName', 
    password='yourPassword')
dbExecute(con, query_to_affect_something) # e.g., create a temp table
dbGetQuery(con, query_something_to_return_results)
```

### Some References
* StackOverflow: [Installing RPostgreSQL on Linux](https://stackoverflow.com/questions/22202141/installing-rpostgresql-on-linux)
* [How to download and install prebuilt OpenJDK packages](http://openjdk.java.net/install/)
* [How to Install R on Linux Ubuntu](https://www.r-bloggers.com/how-to-install-r-on-linux-ubuntu-16-04-xenial-xerus/)
