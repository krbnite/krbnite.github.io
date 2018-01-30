---
title: Some Basic Hive Commands for Sanity's Sake
layout: post
---

```python
con = connect_to_hive()
ex = con.execute
from pandas import read_sql_query as qry
```

These commands work with either a Hive or Presto connection: 
```
# List available hive databases 
qry('SHOW DATABASES', con)

# List tables
qry('SHOW TABLES', con)

# Show Table Info (e.g., column data types)
qry('DESCRIBE table_name', con)
```
