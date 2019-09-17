---
title: Hive-Minded Big Data
layout: post
tags: big-data hive databases sql wwe
---

We are starting to store some of our data in Hive tables, which we have access to via a Hive or Presto 
connection.  To dive right in, I turned to a few short courses on Lynda.com.  In this log, I document
a few things from the course 
[Analyzing Big Data with Hive](https://www.lynda.com/Hive-tutorials/Analyzing-Big-Data-Hive/534413-2.html).

In this course, to access Hive tables, they use the Hadoop user interface ("Hue").

### So what is Hive? 

Hive is a SQL-like abstraction allowing for interactive querying of Hadoop data (i.e., data in HDFS).  It
is a data warehouse built atop Hadoop. 

You might be asking why you can't just use Hadoop to query Hadoop, or something vague like that.  It's
important to understand that Hadoop is not a database: it is an ecosystem.  This is not unlike, say, Unix...in that, 
there is the file system ("HDFS") and a bunch of tools to manipulate and utilize that file system.

Think of Hive as the tool in the file system that makes the file system "feel" like a database.


### What is the Point?
This SQL-like interface is very important! 

For example, a data analyst might already know SQL, but not how to use lower-level Hadoop tools, 
so Hive was invented as a way to interact with Hadoop data in a way that felt familiar (no Java necessary).

Furthermore, such an interface to the Hadoop ecosystem has allowed other tools, 
such as Excel, to easily connect to and interact with Hadoop.


### What are Some Limitations of Hive?  
One limitation (if you can call it that) might be that Hive is not full ANSI SQL. This just means that 
some SQL queries you are familiar with in other databases might not work in Hive.  But that shouldn't be
a huge problem or surprise: most databases are not fully compliant with ANSI SQL in their own unique ways, 
and even ANSI SQL-compliant databases often have features that go beyond ANSI SQL. For example, if you 
are familiar with Postgres then move to MySQL, you should expect similar friction.  So this limitation
really isn't all too specific to Hive.

A more important limitation is that Hive is slow: it is designed to scale, not to be fast. There are a few ways
to get Hive to run faster (e.g., run on Tez), which some companies use that are already heavily invested in 
Hive. However, if you're not already heavily invested (i.e., "stuck"), then the advice is not use Hive at 
all. 

Also, Hive is fragile...something about schemas.  

The general sentiment is basically, if you want to use Hive, go back to 2012 or before!

### Are there any competitors to Hive?
Yes! 

Spark is a competitor to Hive in that both offer a SQL-like interface to Hadoop.  However, Spark 
helps get us away from MapReduce as it does not translate the SQL to MapReduce. There is also Impala from 
Cloudera...which also does not require MapReduce.  

If you are not invested in Hive already, the don't use it.  Instead, you would be better off choosing 
Spark.  The good news is, if you really liked HiveQL, then fret not!  Spark has a HiveQL interface to Hadoop,
which makes it feel like you're using Hive anyway.

Then there is Presto.

Facebook released Presto in 2013, and its kind of like the sequel to Hive (there's a bad joke in there 
somewhere).  Presto is fully ANSI SQL and does 
not require you move your data outside of Hadoop.  It is basically an in-memory
processing layer over something like Hive...in that it can access Hive tables.  For the data analyst in
need of interactive querying, this is the way to go.  Much faster than executing HiveQL queries from a Hive
connection. 

Presto certainly better meets my team's needs.  (Here is a Lynda tutorial: 
[Presto Essentials](https://www.lynda.com/course-tutorials/Welcome/553697/607598-4.html).)



--------------------------------------------------------------

## More Lynda Courses
#### Presto
* https://www.lynda.com/course-tutorials/Welcome/553697/607598-4.html

#### Hadoop Basics
* https://www.lynda.com/Hadoop-tutorials/Hadoop-Fundamentals/191942-2.html

####  Hive
* https://www.lynda.com/Hive-tutorials/Analyzing-Big-Data-Hive/534413-2.html

#### More Advanced Hadoop Stuff
* https://www.lynda.com/Hadoop-tutorials/Data-Analysis-Hadoop/460439-2.html
* https://www.lynda.com/Hadoop-tutorials/Extending-Hadoop-Data-Science-Streaming-Spark-Storm-Kafka/516574-2.html
