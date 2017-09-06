---
layout: post
title: Something's in the Soup!
---

Here is a quick 1-2 about [BeautifulSoup](https://beautiful-soup-4.readthedocs.io/en/latest/):


```python
import requests
import bs4

# Scrape YouTube Main Page
page = requests.get("http://www.youtube.com")
page = bs4.BeautifulSoup(page.text, 'lxml')

# Look at the Page Title and HTML Head
page.title
page.head

# Look at first link tag in <head></head>
page.head.link

# Find all link tags
page.head.find_all('link')

# Find first link in body
page.body.a

# Look at first span tag in first link in body
page.body.a.span
```

Using this tag-chaining syntax is nice, but how do you know what children tags exist in a parent tag?
To see what direct children a tag has, view 'em all at once (e.g., `page.body.a.contents`) or access
them in generator fashion (e.g., `[tag for tag in page.body.a.children]`). To see all descendants of 
a tag, generate them with `.descendants`.  One can also look at the parent tag (`tag.parent`) and 
ascendants (`[parent for parent in tag.parents]`).  

And don't forget about siblings:
```python
page.head.meta
page.head.meta.next_sibling
page.head.meta.next_sibling.next_sibling
```

More:
* To look at all child strings associated with a tag: `[tag_str for tag_str in tag.strings]`
* To strip the white space: `[tag_str for tag_str in tag.stripped_strings]`
* The parent of a string is a tag:  tag.string.parent is tag

------------------------------------------------

Need to update this with my other notes.

