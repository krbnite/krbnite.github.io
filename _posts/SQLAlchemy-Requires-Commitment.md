---
layout: post
title: SQLAlchemy Requires Commitment (for Granting Public Access)
---

Connecting to Redshift from R is easy:
```r
con = dbConnect(drv, host=host_production, 
       port='5439',
       dbname='dbName', 
       user='yourUserName', 
       password='yourPassword')
dbExecute(con,query_to_affect_something)
dbGetQuery(con,query_something_to_return_results)
```

This works even when you are granting public access to a table or copying CSV files from S3 to Redshift.

```r
# Initialize table layout
dbExecute(con, "CREATE TABLE my_cool_table (a int, b int, c int)")

# Copy CSV file from S3 to the New Table
dbExecute(con,  "
  COPY my_cool_table FROM
  's3://path/to/my_cool_table.csv'
  iam_role 'super_secret_string_of_letters_and_numbers'
  CSV;
")

# Grant public read permissions!
dbExecute(con, "GRANT SELECT ON my_cool_table TO PUBLIC")
```

Done.

For a long while, despite working with SQLAlchemy and Python, I had to use R just to do stuff like that.  
For some reason, issuing similar GRANT and COPY commands from `con.execute()` do nothing.

Finally, I had enough.  Not sure why it took so long -- the [first link](https://groups.google.com/forum/#!topic/sqlalchemy/UHbvTupE4w0)
that surfaced was a user from 2007 asking about the same thing.  The answer came quickly: for whatever reason, you need to issue
BEGIN and COMMIT statements before and after the GRANT command, respectively.  Same thing for the COPY command.

I'll just leave this here for Future Kevin.

```python
# Initialize table layout
con.execute("""
  CREATE TABLE my_crazy_table (a int, b int, c int); 
""")

# Copy CSV file from S3 to the New Table
con.execute("""
  BEGIN; 
  COPY my_crazy_table FROM
  's3://path/to/my_crazy_table.csv'
  iam_role 'super_secret_string_of_letters_and_numbers'
  CSV; 
  COMMIT;
""")

# Grant public read permissions!
con.execute("""
  BEGIN; 
  GRANT SELECT ON my_crazy_table TO PUBLIC 
  COMMIT;
""")
```
