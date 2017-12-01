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
