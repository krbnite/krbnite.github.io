---
title: How to Add a Column to the Middle of a Redshift Table
layout: post
---

Just going to leave this here for future reference! 

```sql
ALTER TABLE tbl RENAME TO old_tbl;
ALTER TABLE old_tbl ADD COLUMN new_col <dataType>;
UPDATE old_tbl SET new_col=<value> WHERE <condition>;
CREATE TABLE tbl (col1 <dataType>, ..., new_col <dataType>, ..., colk <dataType>);
INSERT INTO tbl (SELECT col1, ..., new_col, ..., colk FROM old_tbl);
DROP TABLE old_tbl;
```

If I already have an article with this in it... Oh well!  Couldn't find it.
