---
title: Cypher Syntax Highlighting for Vim
layout: post
---

Not going to lie to you: this post is not any better than googling "cypher syntax highlighting for vim."  In fact,
that might be a better thing for you to do!  But since you're already here--

```
# Assuming you have Pathogen installed...
cd ~/.vim/bundle
git clone git://github.com/neo4j-contrib/cypher-vim-syntax.git
cd ~/YourCypherProject/
vim hello-world.cypher
```

Consider your syntax highlighted:
```
CREATE (hello:World {greeting: "Earthlings!"})
RETURN 'Aloha '+hello.greeting
```

...in Vim, but not on GitHub :-(

Oh GitHub, why hast thou forsaken me!  (Relevant music plays in the background, reminding the world that System of a Down needs to put out a new album already: "Forsaken me. Forsaken me. Forsaken. Me--! Oh, I--- cry--- when angels deserve to die!") 


## References
Check out the project @ [https://github.com/neo4j-contrib/cypher-vim-syntax](https://github.com/neo4j-contrib/cypher-vim-syntax).

