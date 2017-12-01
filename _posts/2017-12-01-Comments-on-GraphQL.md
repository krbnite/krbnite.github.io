---
title: Comments on GraphQL
post: layout
---

At work, I'm been diving deep on everything and anything Facebook:
* What is the Open Graph Protocol?
* How do I use the Graph API?
* What data can we get from Facebook Insights or Analytics? 
* How about Audience Insights? Automated Insights? Facebook IQ?

The list goes on!

Today I touched on GraphQL.  Figured I'd take some notes and blogify them for future reference.

## What is GraphQL?
[GraphQL](https://github.com/facebook/graphql) is an API standard invented at Facebook in 2012, and 
[open-sourced in 2015](https://code.facebook.com/posts/1691455094417024/graphql-a-data-query-language/). It is
an alternative to the [REST standard](https://www.w3.org/2001/sw/wiki/REST).

Something I thought was interesting is that a GraphQL server exposes only a single endpoint, instead of
multiple endpoints that return fixed data structures. Given the single endpoint, the client can request
that data structure needed for a given situation. This simplifies things!

Riveting Tidbit: At the time of this writing, the Facebook Graph API is a REST API, not GraphQL!  However, 
you can use GraphQL-like syntax when querying.  From the [Graph API Docs](https://developers.facebook.com/docs/graph-api/using-graph-api):

```
GET graph.facebook.com
  /me?fields=albums.limit(5){name,photos.limit(2).after(MTAyMTE1OTQwNDc2MDAxNDkZD){name,picture,tags.limit(2)}},posts.limit(5)
```

## Why use GraphQL?
Also -- Why try to reinvent the explanation? 

Samer Buna deftly explains it at [FreeCodeCamp](https://medium.freecodecamp.org/rest-apis-are-rest-in-peace-apis-long-live-graphql-d412e559d8e4):

> The 3 most important problems that GraphQL solves beautifully are:
> * The need to do multiple round trips to fetch data required by a view: With GraphQL, you can always fetch all the initial data required by a view with a single round-trip to the server. To do the same with a REST API, we need to introduce unstructured parameters and conditions that are hard to manage and scale.
> * Clients dependency on servers: With GraphQL, the client speaks a request language which: 1) eliminates the need for the server to hardcode the shape or size of the data, and 2) decouples clients from servers. This means we can maintain and improve clients separately from servers.
> * The bad front-end developer experience: With GraphQL, developers express the data requirements of their user interfaces using a declarative language. They express what they need, not how to make it available. There is a tight relationship between what data is needed by the UI and the way a developer can express a description of that data in GraphQL .



## Exploring GraphQL on GitHub using GraphiQL
Facebook's Graph API explorer has been helpful with getting up-and-running in previous posts.  Similarly,
I have found Github's GraphQL explorer, [GraphiQL](https://developer.github.com/v4/explorer/),to help get my bearings.

## Recycle, Reduce, Reuse
How many times have you written the same line of code or SQL query?  Often we like to minimize
such redundancy (e.g., with functions or, in SQL, with views). This type of philosophy remains true 
when issuing graph queries! 

In GraphQL, we call a reusable chunk of code a "fragment".  Fragments are reusable graph (sub)queries.  They are important 
for our sanity, e.g., in reducing the size and apparent complexity of a query.

For example, below a fragment is used to simplify a query requesting the same data from mutiple repositories.

```graphql
fragment repoFrag on Repository {
  id
  name
  description
}

query myRepos { 
  blog: repository(name: "krbnite.github.io", owner: "krbnite") {
    ...repoFrag
  }  
  deepL: repository(name: "deep-learning-nanodegree", owner:"krbnite") {
    ...repoFrag
  }
  mushrooms: repository(name: "FungusAmongus", owner: "krbnite") {
    ...repoFrag
  }
}
```

## References
* Lynda's [Learning GraphQL](https://www.lynda.com/JavaScript-tutorials/Learning-GraphQL/574714-2.html)
* [www.howtographql.com](https://www.howtographql.com)
* [https://github.com/facebook/graphql](https://github.com/facebook/graphql)
* [http://graphql.org/](http://graphql.org/)
* https://code.facebook.com/posts/1691455094417024/graphql-a-data-query-language/
* [REST APIs are REST-in-Peace APIs. Long Live GraphQL](https://medium.freecodecamp.org/rest-apis-are-rest-in-peace-apis-long-live-graphql-d412e559d8e4)
