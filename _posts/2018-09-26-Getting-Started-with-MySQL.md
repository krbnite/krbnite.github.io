---
title: Getting Started with MySQL
layout: post
---

At work, we have data assets -- both potential and in-hand -- and a set of business interests and use
cases for these assets.  How complex is this data?  How much of it do we have, or will we potentially
manage in the future?  What is the best way to model and store this data?  These are the types of
questions that I'm now confronted with.  

In my previous job, there were data engineers, data modelers, other data scientists, web developers,
and so on.  I dealt with many issues, but also -- many issues were handled for me, and often without
any consultation on my part.  I often worked with Redshift, but did not set up or manage Redshift.  I
took advantage of an S3 bucket to test out Hive and Presto, but I did not set up Hive or Presto, and
though I toyed with a few SerDe files, I was not responsible for the more complex ones. Moreover, it 
did not matter to me that some of our data was in Redshift, while other data was in S3, on my laptop,
or souced in some other way: I simply glued everything together in Python scripts and thought nothing
more of it.

Alas! I'm in a whole other situation now: I must be the data engineer, the data modeler, and the web
developer.  I must use cloud technologies like S3 or RDS or Aurora, and also must be the one that helps
set these things up.  If we choose a particular storage solution, that choice and its implications are
partly or wholly on me.  And finally, it does not matter that I can glue everything together in a Python
shell: I am responsible for helping make sure the data is accessible to a diverse set of users and use
cases.  

Anyway, that's some context.  More context is that we already have RDS for MySQL set up, and if possible,
our small team has agreed that if we can continue to use MySQL for this new need, then we should for
simplicity's sake.  However, my co-developer and I also suspect the a graph database like Neo4j might
better support our project -- and the snazzy visualization that comes stock with Neo4j nicely matches one of
our visualization requirements.  So, ultimately, we are working on mapping out the entities and relationships,
keeping in mind both a relational and graph model.

Ok, enough context!  The point of this blog is to lightly document downloading and setting up MySQL Server,
MySQL Workbench, and MySQL command line tools.



<img src="/images/getting-started-with-mysql.png" width="500">


<img src="/images/create-new-mysql-schema.png" width="500">
