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


Fragments are important for frequently used queries.  Plug something like this into Github's 
GraphQL explorer, [GraphiQL](https://developer.github.com/v4/explorer/):
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
}
```

