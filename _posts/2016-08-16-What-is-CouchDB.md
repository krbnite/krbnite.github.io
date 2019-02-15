---
title: What is CouchDB?
layout: post
tags: databases
---

When choosing a database, there is MongoDB, Cassandra, MariaDB, PostgreSQL, and on and on.  One question that plagues my mind 
when reading about all these possibilities is:  How do you know which one to use?  What use cases is a particular DB 
optimized for? Which ones is it terrible for?

I stumbled across a great article by [Nolan Lawson](https://nolanlawson.com/) about what people *think* CouchDB is 
(another database) and CouchDB actually is (it is effectively a next-generation web-server).

## CouchDB doesn’t want to be your database. It wants to be your web site.
That is the name of an [article](https://nolanlawson.com/2013/11/15/couchdb-doesnt-want-to-be-your-database-it-wants-to-be-your-web-site/)
by Lawson.  The name itself says it all, but makes more sense with some context.  Here are some quotes from the 
article that I found helpful in understanding what CouchDB is all about.

> CouchDB is a NoSQL database (or key-value store, as the cool kids say) written in Erlang. It is probably the origin of this joke. Nobody who uses CouchDB cares that it is written in Erlang, though, because the big selling point is that you can interact with it using Javascript, JSON, and plain ol’ HTTP. It is "a database for the web," the first of its kind.

Lawson says that, at first, "the fact that [CouchDB] ran in a web browser just kinda seemed like a gimmick."  However, 
after seeing the following quote, Lawson realized what CouchDB was all about:

> "Because CouchDB is a web server, you can serve applications directly [to] the browser without any middle tier. When I'm feeling punchy, I like to call the traditional application server stack "extra code to make CouchDB uglier and slower."

More quotes from Lawson's article:

> I think the reason a lot of developers, like myself, might have missed this epiphany is that we're used to treating databases as, well, databases. Whether it's MongoDB or MySQL or Oracle, you gotta have your JDBC connector for Java and perhaps an ORM layer or maybe you just give up on Hibernate and write all the database objects yourself, so half of your code is getters and setters, but that’s OK, because that’s how we abstract the database.  ...  But you don't need that with CouchDB. Because… it's just HTTP. Any extra layers just give you another API to learn.

And finally:
> Despite [some] drawbacks, I still think CouchDB has a lot of potential to revolutionize the way people write webapps. ... When you step back and look at it, it's a daring, crazy proposition, a bold statement about how awesome web development would be if we could just let it be the web. It's a raving streetside lunatic, grabbing random people by the shoulders and screaming at them with frantic urgency: "We don't need the server anymore! We only need the database! The database is the server!"
>
> In short, CouchDB is an expression of an ideal, a fantastical tale of science fiction told by wide-eyed dreamers. And if there's one truth about wide-eyed dreamers, it's this: with hindsight, their predictions either seem delusional, or inevitable.

