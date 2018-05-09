---
title: Instagraph Strategy (Part 1)&#58; Everything But the Webhooks
layout: post
---

Instagram has a few challenges, but seems to be a little simpler to strategize than Facebook
given its small number of nodes, edges, fields, insights, and so on.  

One challenge is the ephemeral
story, whose insights vanish at the 24-hour mark.  To ensure one captures a story's end-of-life 
insights reasonably using the classical API approach, we would need to set the collection cadence for 
stories at an hour or better!  To really capture a story's insights, however, one should take
the recommended approach: webhooks.  I'll look into that and discuss it further in a second
part to this post.

Another challenge that has occurred to me is in the mass collection of an account's
media nodes along with their fields and edges:
* The fields aren't a problem, as I showed at the end of my previous "Instagraph" post -- completely consistent!  
* The comments edge is not an issue because I plan on collecting comments in a separate process (at least that's
the current plan, though I can imagine keeping up with new comments in a daily pull once I've done
the backfill...still, probably not a problem). 
* The children edge isn't much of problem either.  Perhaps, the only issue with this edge is ambiguity: is it an edge or a 
field?!  Node introspection (`{media_id}?metadata=1`) and the [documentation](https://developers.facebook.com/docs/instagram-api/reference/media/children)
tell us it's an edge, yet other parts of the documentation seem to view it as a field
(e.g., [instagram-api/reference/media](https://developers.facebook.com/docs/instagram-api/reference/media/)). In a
way, it doesn't matter, especially considering that [field expansion](https://developers.facebook.com/docs/graph-api/using-graph-api)
of an edge makes it pretty much like a field.

So what's the problem?

It's those dang insights: if you request an metric not supported by a node, an error will be returned.



-----------------------------------------------------

## Some References
* https://developers.facebook.com/docs/instagram-api/reference/media/
* https://developers.facebook.com/docs/instagram-api/reference/media/children
* https://developers.facebook.com/docs/graph-api/using-graph-api
