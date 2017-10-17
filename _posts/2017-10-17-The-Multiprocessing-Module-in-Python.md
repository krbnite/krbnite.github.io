---
title: The `multiprocessing` Module in Python
layout: post
---

This.

```python
from multiprocessing import Pool
p = Pool(5)
def square(x): return x*x
p.map(square, [2,3,4])
p.map(square, [10,21,43])
# After you're done playing
p.terminate()
```

I probably should expand on this, eh?
