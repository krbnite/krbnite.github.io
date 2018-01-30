---
title: Some Basic Hive Commands for Sanity's Sake
layout: post
---

```python
con = connect_to_hive()
ex = con.execute
from pandas import read_sql_query as qry
```


```
# List available hive databases 
qry('SHOW DATABASES', con)

# List tables
qry('SHOW TABLES', con)
```
