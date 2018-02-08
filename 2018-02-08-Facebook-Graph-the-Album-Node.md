---
title: Facebook Graph&#58; The Album Node
layout: post
---

The Page/Albums edge allowed us to get a list of all Album nodes associated with the
Page node.  From this, we created a simple mapping table. Also, we found that it was possible
to return each album's fields from the Page/Albums edge, and so we created an album fields 
table.  The two tables were kept separate to avoid


# The Album Engagement Table

The album node's engagement edges (/comments, /likes, and /reaction) allow one to get 
a total\_count by setting the summary parameter, `summary=true`. By default, these edges
return a list of the engaged user nodes.  If you want to cut down on the noise and just return 
the summary, then set the limit parameter to zero.

```
token.get(album_id+'/likes?limit=0&summary=true)
```

