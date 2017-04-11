---
layout: post
title: Conditional Aggregation in {dplyr} and Redshift
---

The responsibilities of my job and the projects I work on can vary from one day to the next. Turns out that a clever solution to a problem isn't something I necessarily remember 100% when confronted with the same issue several weeks or months down the line.  

A great example is what you might call "generalized conditional aggregation." 
In a simple conditional aggregation, you might "aggregate the values in column A for only
those records where the value in column B meets some condition," or 
"aggregate all features in Table T for only those records where column B and column
C meet some conditions."

Generalized conditional aggregation isn't so simple.  This is the scenario where
you generally want to group by some variable(s) and aggregate, but have a few
columns that require specific conditions:  "Aggregate all features in Table T;
for feature F1, do so only for condition C1; for feature F2, do so only for feature F2; ..."

## "Small Data"
If you're using R, have your entire data set loaded, and dplyr aggregations are not taking forever,
then awesome -- here's what you do.  

Say you have a table like this in R that you've queried from some database
that hosts streaming data:

```{r}
streaming_info = dbGetQuery(con, "
  SELECT 
    cust_id,
    month,
    orderStatus,
    content,
    stream_type,
    start_time::date AS ymd,
    SUM(play_time) AS play_time
  FROM viewership_table
    WHERE start_time BETWEEN t0 AND tf
  GROUP BY 1,2,3,4,5,6
    ")
```

### Short way: subset variable inside aggregation function
```{r}
agg1 = streaming_info %>% 
  group_by(month, orderStatus, content) %>% 
  summarize(
    on_demand = sum(play_time[stream_type=="od"]), 
    all_time = sum(play_time)
  ) %>%
  arrange(month, orderStatus, content)
```

### Longer, But (Maybe) More Intuitive Way:  Define new variable before aggregation
```{r}
agg2 = streaming_info %>% 
  mutate(on_demand = ifelse(stream_type=="od", play_time, 0)) %>% 
  group_by(month, orderStatus, content) %>% 
  summarize(
    on_demand = sum(on_demand), 
    all_time = sum(play_time)
  ) %>%
  arrange(month, orderStatus, content)
```

Both methods give the same results... Choose whichever you remember at the moment, or like.


## "Big Data"
One thing I learned working with large data sets from the WWE Network is that
an unplanned, foolish query to Redshift from RStudio on my laptop can make things pretty dang sluggish.  

```{r}
# e.g., this can be bad :-p
dbGetQuery(con, "SELECT * FROM bigAssTable;")
```

A great rule-of-thumb is that any aggregation which can be executed remotely in Redshift prior
to loading into R, should be! (This goes for other environment too, e.g., when developing a
Tableau dashboard, this is an excellent performance booster.)

To conditionally aggregate a specific feature in Redshift, you just have to remember to put the CASE 
statement inside the aggregation function. It's simple, but I've definitely forgotten it multiple times,
thus this blog article.  If you put the aggregation function inside the CASE statement instead, you will 
get errors.

```{r}
agg3 = qry(con, "
  SELECT 
    month,
    orderStatus,
    content,
    SUM( CASE 
      WHEN stream_type='od' THEN play_time
      ELSE 0 END)
      AS on_demand,
    SUM(play_time) all_time
  FROM viewership_table 
    WHERE start_time BETWEEN t0 AND tf
  GROUP BY 1,2,3
    ORDER BY 1,2,3
    ") 
```


