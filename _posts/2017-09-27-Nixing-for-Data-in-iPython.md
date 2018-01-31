---
title: Nixing for Data in iPython
layout: post
---

When using Jupyter Notebooks (or iPython/Jupyter console/Qt), I take advantage of the fact that you natively 
use Unix/Linux commands.

For example, what files are in this directory again?
```python
ls
```

What data did that file have in it?
```
cat data.csv
```

For most commands, you need to use the bang operator (like in Vim):
```python
!head data.csv
```

I sometimes feel uneasy doing this over the more platform-independent options available in
packages like os and sys.  But that feeling of unease pales in comparison to the unease I feel
at having figured out this:

```python
file = !cat file.txt
xyz = !head -n 50 data.csv | grep "^kangaroo"
```

This is mad!  But so F'n cool. Yet -- ick, not to be trusted, right?!  I mean, it's one thing to use
it doing quick-and-dirty, exploratory stuff... But the idea of getting to comfy with this is unsettling: Will
it always work? What if my script gets ported to a Windows machine?  Why not just do it the regular Python
way?

Yet you cannot deny the possibilities!  Automatically, you have things like Awk and Sed at your finger tips
from within Python, to slice and dice some files before bringing 'em in.  



