---
title: SQLAlchemy LIKE &#39;%%WTF%%&#39;
layout: post
tags: python aws redshift databases wwe
---

This is just a public service anouncement.  If you are using SQLAlchemy to connect to Redshift and you
issue a LIKE statement using the % wildcard, you will confront some difficulty. This is because the % symbol
is special to both SQLAlchemy (escape symbol) and Redshift's LIKE statement (wildcard). In short, your
LIKE statements should look more like this:
```python
con.execute("""
  SELECT * FROM table WHERE someVar LIKE '%%wtf%%'
""")
```
