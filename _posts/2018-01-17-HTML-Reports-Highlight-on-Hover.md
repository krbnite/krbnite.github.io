---
title: HTML Reports&#58; Highlight on Hover
layout: post
tags: python webdev automation wwe
---

While trying to figure out how to ensure that the HTML report's numeric values
had commas is select columns, I stumbled across a neat little tidbit in the
documentation for `styles.set_table_style()` that gives the email an interactive
vibe: highlight the row that the cursor is resting on.  

This also helped me better understand what was going on with my original
styles object:

```python
styles = [dict(selector = 'th:first-child', props = [('display', 'none')])]
```

At first glance, that thing is a nasty sombitch. Why so many objects and brackets? Tuples
in lists in dictionaries in lists? Wtf.

I'll tell you wtf: this maps to what you would write in a CSS file. To see it, it's 
better written like so:

```python
styles = [
  dict(
    selector = 'th:first-child', 
    props = [('display', 'none')]
  )
]
```

In a CSS file, this \*might\* look like:

```css
th:first-child {
  display: none;
}
```

I'm rusty on CSS, so... Anyway, you get the point! You have CSS selectors and their properties. Each property
has a value, thus for the props key, we have a list of tuples of (property, value) pairs.

## Just Tell Me How To Highlight a Row Already!
Easy! I just added the another selector to my CSS object.

```python
styles = [
  dict(
    selector = 'th:first-child', 
    props = [('display', 'none')]
  ),
  dict(
    selector = 'tr:hover',
    props = [('background-color', 'yellow')]
  )
]

# Render the DataFrame
HTML = df.style.set_table_style(styles).render()
```
