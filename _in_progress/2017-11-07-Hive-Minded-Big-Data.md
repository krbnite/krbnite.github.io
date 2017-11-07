
Reference:  https://www.lynda.com/Hive-tutorials/Analyzing-Big-Data-Hive/534413-2.html

* Hadoop user interface ("Hue")

Def: Hive: a SQL-like abstraction allowing for interactive querying of Hadoop data (i.e., data in HDFS)

Point: A data analyst might already know SQL, but not how to use lower-level Hadoop tools, 
so Hive was invented as a way to interact with Hadoop data in a way that felt familiar (no Java necessary)

Importance: Hive SQL-like interface to the Hadoop ecosystem has allowed other tools, such as Excel, to
easily connect to and interact with Hadoop.

Hadoop is not a database: it is an ecosystem. Not unlike, say, Unix...in that, there is the file system,
HDFS, and then a bunch of tools to manipulate and utilize that file system.

Limitations:  
* Hive is not full ANSI SQL, which means that some SQL queries you are familiar with in other databases 
might not work in Hive (that said, even for ANSI SQL databases, there are often features that go beyond ANSI SQL...so
you can kind of say this about migrating to a different tool in general).
* Apparently, Hive is slow: it is designed to scale, not to be fast
* Hive is fragile...something about schemas

Did you know: Spark is a competitor to Hive in that both offer a SQL-like interface to Hadoop.  However, Spark 
helps get us away from MapReduce as it does not translate the SQL to MapReduce...  If you really wanted to shun
Spark, you could use Impala from Cloudera...which also does not require MapReduce.  There are a few ways
to get Hive to run faster (e.g., run on Tez), which some companies use that are already heavily invested in Hive, but if you're not
already stuck using Hive, you should probably use Spark (which has a HiveQL implementation if you really want
to feel like you're using Hive).

Apparently, Presto is fully ANSI SQL and does not require you move your data outside of Hadoop. 

* Basically, if you want to use Hive, go back to 2012 or before! Facebook released Presto in 2013 and it is
a much better tool for our needs
  - https://www.lynda.com/course-tutorials/Welcome/553697/607598-4.html

--------------------------------------------------------------

Presto
* https://www.lynda.com/course-tutorials/Welcome/553697/607598-4.html


Hadoop Basics
* https://www.lynda.com/Hadoop-tutorials/Hadoop-Fundamentals/191942-2.html

On Hive
* https://www.lynda.com/Hive-tutorials/Analyzing-Big-Data-Hive/534413-2.html

More Advanced
* https://www.lynda.com/Hadoop-tutorials/Data-Analysis-Hadoop/460439-2.html
* https://www.lynda.com/Hadoop-tutorials/Extending-Hadoop-Data-Science-Streaming-Spark-Storm-Kafka/516574-2.html
